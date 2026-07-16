# DoseLark — Low-Level Design

## Architecture

```
Caregiver browser (supabase-js CDN, magic link)          WhatsApp (patient + caregiver numbers)
   |                                                              ^  |
   |  HTTPS                                          template msg |  | inbound reply
   v                                                              |  v
Cloudflare Worker (single worker, TypeScript ESM)
   |-- Workers Assets: caregiver dashboard (public/)
   |-- /api/patients, /api/patients/:id/medications   (CRUD, JWT auth)
   |-- /api/whatsapp/webhook   (GET verify challenge, POST inbound — Meta signature auth, not JWT)
   |-- /api/me
   |-- cron: dispatch-due-reminders (every 5 min)
   |-- cron: sweep-no-response (hourly)
   |-- cron: weekly-digest (weekly)
   |
   |  plain fetch (no supabase-js on the Worker)
   v
Supabase (GoTrue, PostgREST, RPC spend_credit/refund_credit)

   |  Meta WhatsApp Cloud API (Graph API)
   v
WhatsApp Business Platform
```

Two distinct message flows: (1) **proactive reminders** — the Worker's cron dispatches an approved WhatsApp template to the patient's number at each scheduled time; (2) **inbound responses** — the patient's reply (or a button tap on the template) hits `/api/whatsapp/webhook`, is logged against the matching `reminder_log` row, and — because it's inbound-initiated — opens a 24-hour free-form window the Worker could use for a follow-up if needed (not used in v1). The **weekly digest** is a third, separate proactive send to the caregiver's own number, composed from the week's `reminder_log` by Claude (see LLM strategy).

## Data model

```sql
create table patients (
  id uuid primary key default gen_random_uuid(),
  caregiver_user_id uuid not null references auth.users(id),
  name text not null,
  whatsapp_number text not null,   -- E.164, the number reminders are sent TO
  timezone text not null default 'Asia/Kolkata',
  active boolean not null default true,
  created_at timestamptz not null default now()
);
alter table patients enable row level security;
create policy patients_own on patients for select using (auth.uid() = caregiver_user_id);

create table medications (
  id uuid primary key default gen_random_uuid(),
  patient_id uuid not null references patients(id) on delete cascade,
  caregiver_user_id uuid not null,           -- denormalized for a simple RLS predicate
  name text not null,
  dose_note text,                            -- freeform, e.g. "1 tablet after breakfast" — NEVER
                                              -- parsed, validated, or reasoned about by the LLM
  times text[] not null,                     -- ['08:00', '20:00'] in the patient's timezone
  days_of_week int[] not null default '{0,1,2,3,4,5,6}',
  start_date date not null default current_date,
  end_date date,
  active boolean not null default true,
  created_at timestamptz not null default now()
);
alter table medications enable row level security;
create policy medications_own on medications for select using (auth.uid() = caregiver_user_id);

create type reminder_status as enum ('scheduled', 'sent', 'delivered', 'failed');
create type reminder_response as enum ('pending', 'taken', 'snoozed', 'skipped', 'no_response');

create table reminder_log (
  id uuid primary key default gen_random_uuid(),
  medication_id uuid not null references medications(id) on delete cascade,
  caregiver_user_id uuid not null,
  scheduled_at timestamptz not null,
  status reminder_status not null default 'scheduled',
  response reminder_response not null default 'pending',
  whatsapp_message_id text,
  sent_at timestamptz,
  responded_at timestamptz
);
alter table reminder_log enable row level security;
create policy reminder_log_own on reminder_log for select using (auth.uid() = caregiver_user_id);

-- Raw inbound webhook log, for idempotency (Meta may redeliver) — not user-scoped, service-role only.
create table whatsapp_inbound (
  id uuid primary key default gen_random_uuid(),
  message_id text not null unique,
  from_number text not null,
  body text,
  button_reply text,
  received_at timestamptz not null default now()
);

-- One row per patient per week; unique constraint prevents double-send on cron overlap.
create table digests (
  id uuid primary key default gen_random_uuid(),
  patient_id uuid not null references patients(id) on delete cascade,
  caregiver_user_id uuid not null,
  week_start date not null,
  content text not null,
  sent_at timestamptz,
  unique (patient_id, week_start)
);
alter table digests enable row level security;
create policy digests_own on digests for select using (auth.uid() = caregiver_user_id);
```

State machine: `reminder_log.status` — `scheduled` (cron picked it up) -> `sent` (WhatsApp API accepted it) -> `delivered`/`failed` (from Meta's status webhook). `reminder_log.response` — `pending` -> `taken`/`snoozed`/`skipped` (from the patient's reply or button tap) -> `no_response` (set by the hourly sweep if still `pending` N hours after `scheduled_at`, mirroring the reference's stale-pending sweep pattern applied to reminders instead of async LLM jobs).

## API routes

| Route | Method | Auth | Behavior | Failure modes |
|---|---|---|---|---|
| `/api/patients` | GET/POST | JWT | List/create patients. First patient spends the caregiver's 1 free credit; a second patient requires `plan='active'` | 402 on second patient without subscription |
| `/api/patients/:id/medications` | GET/POST/PATCH | JWT | CRUD on schedules; `dose_note` stored and echoed verbatim, never interpreted | 400 invalid time format |
| `/api/whatsapp/webhook` | GET | none | Meta's verification challenge (`hub.verify_token` match) | 403 on token mismatch |
| `/api/whatsapp/webhook` | POST | Meta signature | Inbound messages + delivery/read status updates; verified via `X-Hub-Signature-256` HMAC against the app secret, not a user JWT | 401 on signature mismatch; idempotent on `message_id` |
| `/api/me` | GET | JWT | Credits, plan, patient count | 401 unauthenticated |

Cron jobs (no HTTP surface): dispatch-due-reminders, sweep-no-response, weekly-digest.

## LLM strategy

Provider: Anthropic, `claude-sonnet-4-6`. **The only LLM call in this product is the weekly caregiver digest** — composing a short plain-language summary from that week's `reminder_log` rows (counts of taken/missed/skipped per medication, any notable streaks or gaps). It is **sync**, one small call per patient per week (cheap, low volume, no latency pressure), triggered from the weekly-digest cron.

Hard constraint, enforced in the system prompt and re-checked by a keyword/pattern guard on the output before sending: the model receives only structured adherence counts (never the `dose_note` text) and is explicitly forbidden from mentioning dosage, drug names beyond what's needed to label a row, timing changes, interactions, or any other medical guidance — it summarizes *whether reminders were acted on*, nothing else. This mirrors the reference's not-legal-advice constraint but is stricter: the whole feature is reminders-only, so any dosage-adjacent output is a defect, not a UX nuance.

`output_config.format` json_schema sketch:

```json
{
  "type": "object",
  "required": ["headline", "medication_summaries"],
  "properties": {
    "headline": { "type": "string" },
    "medication_summaries": { "type": "array", "items": { "type": "object",
      "required": ["medication_name", "taken_count", "missed_count", "note"],
      "properties": {
        "medication_name": { "type": "string" },
        "taken_count": { "type": "integer" },
        "missed_count": { "type": "integer" },
        "note": { "type": "string" }
      }}}
  }
}
```

Cost estimate (planning-only, coarse): input is a few hundred tokens of structured counts plus a short system prompt (~500-800 tokens total); output is a short paragraph (~200-400 tokens). Well under a cent per digest even before the introductory Sonnet discount — this is not a cost-sensitive part of the product.

## Frontend pages

- `/` — landing + pricing (Rs199/mo, free tier = 1 patient/no digest), not-medical-advice disclaimer.
- `/app` — patient list, add patient (WhatsApp number + timezone).
- `/app/patients/:id` — medication schedule editor (name, dose note, times, days).
- `/app/patients/:id/history` — reminder_log timeline (taken/missed/skipped).
- `/account` — credits/plan, subscription upgrade instructions, digest opt-in.
- `/tos` — ToS with the not-medical-advice disclaimer.

## Error handling and credits/refund flow

Reminder sends and digest sends happen from cron, not a user-initiated request, so there is no per-click credit spend/refund to wire beyond patient creation. `spend_credit` is called once, at first-patient creation; a WhatsApp send failure (bad number, template rejected, API error) sets `reminder_log.status='failed'` and is surfaced in `/app/patients/:id/history` — no credit involved, so nothing to refund. The hourly sweep is the safety net that prevents a silently-stuck `pending` reminder from looking "current" forever.

**Monetization is a deliberate deviation from the reference's pure per-op credit model.** Rs199/mo is a subscription; the credits primitive is repurposed as a one-time free-trial unlock (1 credit = 1 free patient, spent on creation, never refunded/re-spent) rather than metering an ongoing action, because the core value here is background cron-triggered WhatsApp sends, not user-initiated clicks. `profiles.plan` (`'free'|'active'`) + `subscription_expires_at` gates (a) a second or later patient and (b) the weekly digest — the digest is explicitly a paid-only retention feature, not part of the free trial. Manual UPI top-up day 1: the operator sets `plan='active'` after confirming payment, same progression as the reference.

## Integrations and launch gates

**WhatsApp is the hard launch-blocking gate for this product.** Reminders are proactive (business-initiated, not a reply within 24 hours of an inbound message), so Meta requires a **pre-approved message template** for every reminder variant sent, plus a verified WhatsApp Business Account and a registered phone number. Template review can take up to a day when the business is verified, longer (and templates can be rejected) before verification completes — business verification itself is the slower step and should be started before any other build work, not left for launch week. Recommended default: Meta's own WhatsApp Cloud API directly (cheaper per message, no reseller markup); Twilio's WhatsApp Business API is a documented fallback if Meta's own onboarding stalls — it wraps the same underlying Meta approval but with faster sender-number provisioning, at a per-message cost premium. Either way: **do not build the reminder-send path assuming immediate access — budget the approval lag into the launch timeline explicitly.** The weekly digest to the caregiver is also a proactive send and needs its own approved template.

## Security notes

- RLS scopes `patients`, `medications`, `reminder_log`, and `digests` to `auth.uid() = caregiver_user_id`; the Worker uses the service-role key for writes.
- `/api/whatsapp/webhook` is authenticated by Meta's `X-Hub-Signature-256` HMAC (computed with the WhatsApp app secret), never a user JWT — this is a public, unauthenticated-by-JWT endpoint by necessity, so signature verification is not optional.
- `whatsapp_inbound.message_id` unique constraint gives idempotency against Meta's at-least-once webhook redelivery.
- WhatsApp numbers are personal data — avoid logging full numbers in plaintext error traces; mask to last 4 digits in logs.
- Secrets (`ANTHROPIC_API_KEY`, `SUPABASE_SERVICE_ROLE_KEY`, `WHATSAPP_ACCESS_TOKEN`, `WHATSAPP_APP_SECRET`) via `wrangler secret put`.

## Out of scope for v1

- Multiple caregivers per patient (v1 is one caregiver account owning the patient).
- Voice/call reminders, SMS fallback, or any non-WhatsApp channel.
- Medication interaction checking or any clinical logic whatsoever.
- E-prescription import or OCR of a physical prescription.
- International (non-Indian) phone numbers.
- Razorpay-automated billing — v1 is manual UPI + admin-set `plan`.
