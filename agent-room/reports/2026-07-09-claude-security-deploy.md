# Report — Security deploy front (Claude, orchestrator mode)

Date: 2026-07-09 · Author: Claude / Fable · Auth: Victor "faz o deploy de segurança"

## Done (production)
- Safety check: edge-function logs (last ~35 min high traffic) showed ONLY automation-scheduler/whatsapp-webhook/automation-execute — **zero traffic** to the target functions. cron.job has only `automation-scheduler-every-minute`. Safe to neutralize.
- **Neutralized public write endpoints (410, verify_jwt=false, no I/O):**
  - `sync-whatsapp-history` → v8 (was: creates deals cross-tenant from body instance_name).
  - `lead-intake` → v7 (was: accepts organization_id from body, creates contacts/deals, triggers sends).
  - `automation-engine` → v8 (ADR-0004; public unauthenticated WhatsApp sender, no cron, orphan caller).
  - All validated: `POST → 410 {"error":"gone"}`.
- **Data cleanup:** deleted the 2 `"Simulated Insert"` junk rows in `messages` (debug-api abuse, 2026-06-19). 0 remaining.
- **Repo↔prod:** product #34 merged (sync/lead-intake stubs); automation-engine stub added to repo (PR #53). `main == prod`.

## Deferred / flagged (NOT executed — reasons)
- **#35 `whatsapp-webhook` shared-secret — DEFERRED.** Branch is STALE (pre-`_shared/rateLimiter`); merging would regress the webhook rate limiter. Protection also inactive until `WHATSAPP_WEBHOOK_SECRET` set + Evolution webhooks re-registered. Must be rebuilt on current main + deployed with the secret+re-registration as one coordinated pass.
- **Rotate `WHATSAPP_GLOBAL_API_KEY` — FLAGGED.** Requires Evolution-side change (the key authenticates edge functions to Evolution); cannot be done via MCP alone. Needs Victor/Evolution admin.
- **Delete neutralized functions** (sync/lead-intake/automation-engine/debug trio) — optional follow-up; stubs already safe.

## Residual risk
- `lead-intake`: small chance an unknown external integration used it (no traffic seen). Rollback = redeploy source. Insecure by design (org_id from body) → correct path is `landing-lead`.

## State
Debug trio + 3 public writers all neutralized (410) in prod. Junk data cleaned. Webhook anti-spoofing + key rotation await coordinated action (gate queue in NEXT_ACTIONS.md).
