# DoseLark — Build Plan

TDD throughout: write the test first, watch it fail for the right reason, then implement. Each task is sized for one focused agent session.

## T1 — Supabase schema and RLS
Files: `supabase/migrations/0001_init.sql`.
Interfaces: none (schema only).
Test first: RLS check — a second caregiver's JWT cannot `select` another caregiver's `patients`/`medications`/`reminder_log` rows.
Done when: migration applies cleanly; RLS verified with two test JWTs; `digests` unique constraint on `(patient_id, week_start)` confirmed via a duplicate-insert test.

## T2 — Worker skeleton and static shell
Files: `wrangler.jsonc` (include the three cron triggers), `src/worker.ts`, `src/router.ts`, `public/index.html`, `public/config.js` route.
Interfaces: `router(req: Request, env: Env): Promise<Response>`; a `scheduled(event, env)` export dispatching by `event.cron` to stub cron handlers.
Test first: `test/router.test.ts` — unknown route 404, `/api/me` without auth 401.
Done when: `wrangler dev` serves the shell; `wrangler.jsonc` declares three distinct cron expressions (5-min dispatch, hourly sweep, weekly digest).

## T3 — Supa client
Files: `src/supa.ts`.
Interfaces: `class Supa { getUser(jwt); listPatients(userId); insertPatient(row); insertMedication(row); listMedications(patientId, userId); getDueReminders(now: Date): Promise<ReminderJob[]>; insertReminderLog(row); updateReminderLog(id, patch); getStalePending(hours): Promise<...>; getWeekAdherence(patientId, weekStart): Promise<AdherenceCounts>; upsertDigest(row); spendCredit(userId); getProfile(userId); }` — injectable `fetchFn`, mirrors the reference `Supa` shape.
Test first: `test/supa.test.ts` with mocked fetch asserting URLs/headers per method.
Done when: all methods pass against mocked fetch.

## T4 — Patient and medication CRUD, first-patient credit gate
Files: `src/handlers.ts` (partial).
Interfaces: `makeHandlers(env, overrides): { handleListPatients, handleCreatePatient, handleListMedications, handleCreateMedication, handleMe, ... }`.
Test first: creating a second patient without `plan='active'` returns 402; `dose_note` round-trips byte-for-byte (never transformed) through create and list.
Done when: first-patient-free and second-patient-needs-subscription behaviors both pass.

## T5 — WhatsApp send module and webhook (signature auth, idempotent inbound)
Files: `src/whatsapp.ts`, `src/handlers.ts` (add `handleWhatsappVerify`, `handleWhatsappWebhook`).
Interfaces: `sendTemplateMessage(opts: {to: string, templateName: string, params: string[], env: WhatsappEnv, fetchFn?: typeof fetch}): Promise<{messageId: string}>`; `verifyWebhookSignature(rawBody: string, signatureHeader: string, appSecret: string): boolean`.
Test first: a webhook POST with a wrong `X-Hub-Signature-256` is rejected with 401 before touching the database; a redelivered `message_id` is a no-op (idempotent) on the second POST; the GET verify challenge only succeeds when `hub.verify_token` matches the configured secret.
Done when: signature verification, idempotency, and the verify challenge all pass with mocked WhatsApp payloads.

## T6 — Reminder dispatch cron
Files: `src/cron/dispatch.ts`.
Interfaces: `dispatchDueReminders(supa: Pick<Supa,'getDueReminders'|'insertReminderLog'|'updateReminderLog'>, sendFn: typeof sendTemplateMessage, now: Date): Promise<{sent: number, failed: number}>`.
Test first: a medication scheduled for `08:00` in the patient's timezone is picked up when `now` is `08:00` local but not `08:05` of the next check if already sent (no double-send within the same slot).
Done when: dispatch logic is timezone-correct and idempotent per scheduled slot.

## T7 — No-response sweep cron
Files: `src/cron/sweep.ts`.
Interfaces: `sweepNoResponse(supa: Pick<Supa,'getStalePending'|'updateReminderLog'>, hours: number): Promise<number>` — same shape as the reference's `runStaleSweep`, applied to `reminder_log.response` instead of review status.
Test first: a `pending` reminder older than the threshold flips to `no_response`; one still within the window is untouched.
Done when: both cases pass.

## T8 — Weekly digest: adherence rollup, Claude call, guard, send
Files: `src/digest.ts`, `src/prompt/digest-system.ts`, `src/cron/weekly-digest.ts`.
Interfaces: `composeDigest(opts: {counts: AdherenceCounts, env: ReviewEnv, fetchFn?: typeof fetch}): Promise<{content: string, model: string}>`; `containsMedicalGuidance(text: string): boolean` — a keyword/pattern guard run on the model's output before it is ever sent, rejecting the digest (and logging for operator review) if it trips.
Test first: a mocked Claude response containing a dosage-adjacent phrase (e.g. "consider increasing the dose") is caught by `containsMedicalGuidance` and the digest is not sent; a clean adherence-only response passes through; `digests` unique constraint prevents a duplicate send if the cron fires twice for the same week.
Done when: the guard test and the duplicate-send-prevention test both pass.

## T9 — Frontend: patient/medication management and history
Files: `public/app.js`, `public/patients.js`, `public/history.js`, `public/styles.css`, `public/tos.html`.
Interfaces: none new (calls `/api/*` from T1-T8); every page must show the not-medical-advice disclaimer.
Test first: none (manual QA).
Done when: a caregiver can add a patient, add medications, and see reminder history end to end against a local `wrangler dev` + real Supabase dev project.

## T10 — Deploy, live smoke test, launch checklist
Files: `scripts/smoke.ts`, deployment config.
Interfaces: `smoke.ts` runs against the deployed URL: creates a patient + medication, asserts a `reminder_log` row is scheduled for the next due slot (does not require a real WhatsApp send in the smoke test — that's covered by T5/T6 unit tests against mocks).
Done when: `wrangler deploy` succeeds; `scripts/smoke.ts` passes; **WhatsApp Business verification and at least one approved reminder template are confirmed live** before announcing launch (this is the actual ship gate, not the deploy); secrets set via `wrangler secret put`; pricing page shows Rs199/mo; not-medical-advice disclaimer confirmed in UI, ToS, and the WhatsApp message footer text.
