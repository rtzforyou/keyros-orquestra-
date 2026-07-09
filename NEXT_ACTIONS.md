# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

Victor approved moving forward.

Agents must use autonomous work-queue mode and checkpoint frequently.

A finished sprint, opened draft PR or report is not a stop condition. It is a checkpoint.

---

# Global Autonomous Rule

Agents must run in continuous work-queue mode:

```text
eligible work
  ↓
implementation batch
  ↓
small commits
  ↓
draft PR checkpoint
  ↓
report checkpoint
  ↓
continue automatically when safe
```

Stop only for a Hard Gate, ownership conflict, blocked work, uncertainty, no remaining eligible work, or the continuous work limit in `GATES.md`.

Draft PRs and reports must be created early to prevent large hidden branches, but they do not require the agent to stop.

---

# CURRENT STATE SNAPSHOT

## Completed / merged

- PR #29 has been merged. Backend resilience repo↔production reconciliation is complete for webhook/send/resilience path.
- `main` was reported healthy after #29: `npx tsc --noEmit` clean and production build successful.
- Critical debug endpoints `debug-whatsapp`, `debug-api`, and `diagnostic` were neutralized in production with 410 Gone kill-stubs.

## Active / open

- PR #31: Antenor clean frontend i18n consolidation. Draft, frontend-only expected, needs review/continuation.
- PR #32: Claude docs/platform inventory. Draft/open docs PR.
- PR #33: security remediation kill-stubs. Draft/open code source for already deployed stubs; must be reviewed/merged/reconciled through normal gate.
- PR #34: security remediation prepared for `sync-whatsapp-history` and `lead-intake`; not deployed.
- PR #35: `whatsapp-webhook` shared-secret hardening; not deployed.
- Engine reports/PRs exist for security, platform docs, and legacy cleanup.

## Blocked / not approved

- PR #30 is not approved. It is stale/dirty and must not be merged. Recover useful frontend work only through clean PRs.

---

# REVIEW / MERGE POSTURE

Victor approved advancing to Phase 3 Product Core.

However, these remain gated and must not be merged/deployed by agents without review:

- PR #33: security kill-stub source reconciliation
- PR #34: `sync-whatsapp-history` / `lead-intake` remediation
- PR #35: webhook shared-secret hardening

Recommended review order when Victor/ChatGPT is actively reviewing:

1. Security PRs first: #33, #34, #35.
2. Frontend PR #31.
3. Docs PR #32.
4. Then larger Phase 3 PRs.

---

# Claude Native Command — Phase 3 Backend + Security Maintenance

## START command

```text
START

Run in continuous autonomous work-queue mode.

Read in order:

1. ROADMAP.md
2. MASTER_STATE.md
3. CLAUDE_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. Relevant docs/knowledge/

Victor approved Phase 3 Product Core direction.

Draft PRs and reports are checkpoints, not stopping conditions.

Continue automatically through eligible backend/platform/security/Product Core backend work inside Claude ownership.

Stop only for Hard Gates, ownership conflict, blocked work, uncertainty, no remaining eligible backend work, or continuous work limit.
```

## Claude primary EPIC — Phase 3 Product Core Backend Foundation

Mission:

Prepare the backend foundation for Product Core without crossing hard gates.

Priority queue:

1. CRM backend/domain audit
2. Contacts backend/domain audit
3. Deals backend/domain audit
4. Dashboard data provider audit
5. Calendar backend audit
6. Automation Engine integration audit
7. Finance domain audit
8. Billing integration points audit without Stripe/provider implementation
9. Repository layer cleanup/design
10. Event bus design/preparation
11. Observability and structured logging
12. Backend tests where safe
13. Documentation and Knowledge Base updates
14. ADRs for Product Core decisions
15. Runbooks for Product Core operations
16. Open/update draft PR
17. Update engine/product report
18. Continue automatically

Rules:

- Do not implement Product Core UI.
- Do not touch Antenor-owned frontend unless explicitly authorized.
- Do not implement Stripe/provider logic.
- Do not apply migrations without gate.
- If a backend improvement requires migration, write the migration and stop before apply.
- If an auth/RLS/permission change is required, stop and report.
- If a major architecture decision is required, write an ADR/proposal and stop before implementation.

Checkpoint with draft PR/report, then continue.

## Claude parallel EPIC — Security Maintenance

Continue the security queue only where safe:

1. Keep PR #33 aligned with deployed debug kill-stubs.
2. Keep PR #34 ready but do not deploy it.
3. Keep PR #35 ready but do not deploy it.
4. Continue classifying public Edge Functions.
5. Prepare safe remediation PRs for risky public functions.
6. Prepare runbooks for key rotation and cleanup, but do not rotate or clean without gate.
7. Document remaining risk.

Hard stop before:

- deploy
- function deletion
- production data cleanup
- key/secret rotation
- migration apply
- auth/RLS/access boundary change

---

# Antenor Native Command — Phase 3 Product Core Frontend

## START command

```text
START

Run in continuous autonomous work-queue mode.

Read in order:

1. ROADMAP.md
2. MASTER_STATE.md
3. ANTENOR_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. docs/knowledge/29-translation-architecture.md
8. Relevant frontend/module docs/knowledge/

Synchronize with latest main before continuing.

Victor approved Phase 3 Product Core direction.

Draft PRs and reports are checkpoints, not stopping conditions.

Continue automatically through eligible frontend/Product Core UI work inside Antenor ownership.

Stop only for Hard Gates, ownership conflict, blocked work, uncertainty, no remaining eligible frontend work, or continuous work limit.
```

## Antenor primary EPIC — Phase 3 Product Core Frontend

Mission:

Build the Product Core UI progressively while keeping frontend/i18n/UX quality high.

Priority queue:

1. Verify and continue PR #31 or create a new clean frontend-only PR if #31 becomes contaminated.
2. Dashboard product polish
3. CRM UI
4. Contacts UI
5. Deals UI
6. Pipeline UI
7. Calendar UI
8. Activities UI
9. Messages UX polish
10. Billing UI consistency without Stripe/provider logic
11. Finance UI shell/presentation only unless backend is ready
12. Mobile UX
13. Responsive improvements
14. Accessibility
15. Component consistency
16. Performance optimization
17. Frontend documentation
18. Open/update draft PR
19. Update engine/product report
20. Continue automatically

Rules:

- Never touch backend.
- Never touch Supabase.
- Never touch Edge Functions.
- Never touch migrations.
- Never touch RLS/auth/permission boundaries.
- Never implement Billing Engine or Stripe logic.
- Do not create fake backend behavior as if real.
- If backend support is missing, show safe empty/coming-soon/read-only states and report the backend blocker.
- All visible UI text must use translation IDs.
- Business logic must use stable IDs/codes, not translated labels.
- Do not translate enums, stage IDs, module keys, route keys or database values.

Checkpoint with draft PR/report, then continue.

## Antenor parallel EPIC — Frontend Quality Maintenance

Continue platform-quality work while building Product Core:

1. Remove remaining hardcoded visible strings.
2. Replace remaining `alert()`, `confirm()`, native prompts and unstyled messages.
3. Improve loading, empty, error, permission-denied, plan-locked and read-only states.
4. Improve modal/drawer accessibility.
5. Improve responsive layout.
6. Update docs only when behavior/rules changed.

Hard stop before:

- backend work
- Supabase changes
- Edge Functions
- migrations
- production deploy
- production data mutation
- auth/RLS/permission boundary change
- Billing Engine / Stripe logic
- destructive operation
- crossing Claude ownership

---

# Native simple START commands for Victor

Claude:

```text
START
```

Antenor:

```text
START
```

The agents must read this file and continue autonomously from the queues above.
