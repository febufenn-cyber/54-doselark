# DoseLark
Medication reminders, refill nudges, and a weekly caregiver summary, delivered over WhatsApp.

**Status: planned — not yet built (50-SaaS challenge #54)**

## The problem
Families managing an elderly parent's medications juggle multiple prescriptions, refill timing, and "did Amma actually take her evening tablet?" — usually tracked in someone's head or a WhatsApp group. Missed doses and late refills are the two most common, most preventable failure modes, and the person managing it (often an adult child, often not living with the parent) has no visibility unless someone texts them.

## Target buyer
Families in India managing elder care, typically an adult child who wants reminders sent to the parent and a weekly rollup sent to themselves — no new app to install for either person.

## Pricing hypothesis
Rs199/month subscription. Free tier: 1 patient, daily reminders, no caregiver digest — the digest is the reason to keep paying.

## Stack
- Cloudflare Worker (TypeScript, Workers Assets) serving the caregiver web dashboard and `/api/*`, plus cron triggers for reminder dispatch and the weekly digest.
- Supabase: magic-link auth for the caregiver, Postgres + RLS, WhatsApp number linked per patient/caregiver.
- Meta WhatsApp Cloud API for message delivery; Claude (`claude-sonnet-4-6`) only for composing the weekly digest text from structured adherence logs — never for anything dosage-related.

## How to continue this build
Read `docs/LLD.md` for architecture, data model, the WhatsApp launch gate, and the LLM strategy, then `docs/PLAN.md` for the ordered TDD task list. `CLAUDE.md` points back to the reference implementation.

## Risks / constraints
- **This product is NOT medical advice.** DoseLark sends reminders only — it never recommends a dosage, timing change, or medication, and the LLM digest prompt is hard-constrained to summarize adherence facts (taken/missed/skipped counts) and never to comment on dosage, drug interactions, or medical appropriateness. This must appear in the UI, the ToS, and every WhatsApp message footer.
- **The caregiver weekly digest is the retention hook** — it is the reason a family keeps paying Rs199/mo after the novelty of reminders wears off, so it is a subscriber-only feature, not part of the free tier.
- **WhatsApp is a launch-blocking integration.** Proactive reminders (sent outside a 24-hour window since the patient last messaged in) require Meta-approved message templates and a verified WhatsApp Business Account — this can take days and templates can be rejected. See `docs/LLD.md` for the full gate and the mitigation (Twilio as a faster-to-approve alternative, at higher per-message cost).
