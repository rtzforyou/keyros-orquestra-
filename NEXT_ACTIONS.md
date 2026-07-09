# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

Victor approved continuing Keyros CRM from the roadmap point where we stopped: **Phase 3 — Product Core**.

Vector is now a separate experiment/repository. This file is only for Keyros CRM execution.

Agents must run in continuous autonomous work-queue mode.

A finished sprint, opened draft PR, or report is a checkpoint, not a stop condition.

---

# Global Rule

Agents continue automatically through eligible work inside their ownership boundary.

Checkpoint loop:

```text
eligible work
  ↓
small implementation batch
  ↓
small commits
  ↓
draft PR checkpoint
  ↓
report checkpoint
  ↓
continue if no Hard Gate exists
```

Stop only for:

- merge to main
- production deploy
- migration apply
- production data mutation
- auth/RLS/access boundary change
- Billing Engine or Stripe implementation
- destructive operation
- ownership conflict
- major architecture decision
- uncertainty
- no remaining eligible work
- continuous work limit from `GATES.md`

---

# Current Keyros State

Completed:

- PR #29 merged. Backend resilience repo↔production reconciliation is complete for webhook/send/resilience path.
- Main was reported healthy after PR #29: typecheck clean and production build successful.
- Critical debug endpoints `debug-whatsapp`, `debug-api`, and `diagnostic` were neutralized in production with 410 Gone kill-stubs.

Open / active:

- PR #31: Antenor clean frontend i18n consolidation.
- PR #32: Claude docs/platform inventory.
- PR #33: security kill-stub source reconciliation.
- PR #34: `sync-whatsapp-history` / `lead-intake` remediation prepared, not deployed.
- PR #35: `whatsapp-webhook` shared-secret hardening prepared, not deployed.

Blocked:

- PR #30 must not be merged. It is stale/dirty. Recover useful frontend work only through clean PRs.

Review priority when Victor/ChatGPT is reviewing:

1. Security PRs: #33, #34, #35.
2. Frontend PR #31.
3. Docs PR #32.
4. Larger Phase 3 PRs.

---

# Claude Command — Keyros Phase 3 Backend

```text
START

Run in continuous autonomous work-queue mode.

Read:
1. ROADMAP.md
2. MASTER_STATE.md
3. CLAUDE_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. Relevant docs/knowledge/

Victor approved Keyros Phase 3 Product Core.

Work only inside Claude ownership: backend, platform, security, Supabase, Edge Functions, database, architecture, documentation.

Draft PRs and reports are checkpoints, not stop conditions.

Continue automatically until a Hard Gate, ownership conflict, uncertainty, no eligible backend work, or continuous work limit is reached.
```

## Claude Phase 3 Queue

Mission: prepare the backend foundation for Product Core.

Priority:

1. CRM backend/domain audit.
2. Contacts backend/domain audit.
3. Deals backend/domain audit.
4. Dashboard data provider audit.
5. Calendar backend audit.
6. Automation Engine integration audit.
7. Finance domain audit.
8. Billing integration points audit without Stripe/provider implementation.
9. Repository layer cleanup/design.
10. Event bus design/preparation.
11. Observability and structured logging.
12. Backend tests where safe.
13. Documentation and Knowledge Base updates.
14. ADRs for Product Core decisions.
15. Runbooks for Product Core operations.
16. Open/update draft PR.
17. Update engine/product report.
18. Continue automatically.

Claude must not:

- implement frontend UI
- touch Antenor-owned frontend without explicit authorization
- implement Stripe/provider logic
- apply migrations without gate
- deploy without gate
- mutate production data without gate
- change auth/RLS/access boundaries without gate

If work requires a migration, write it and stop before apply.

If work requires a major architecture decision, write an ADR/proposal and stop before implementation.

## Claude Security Maintenance Queue

Continue safe security maintenance in parallel:

1. Keep PR #33 aligned with deployed debug kill-stubs.
2. Keep PR #34 ready but do not deploy it.
3. Keep PR #35 ready but do not deploy it.
4. Continue classifying public Edge Functions.
5. Prepare safe remediation PRs for risky public functions.
6. Prepare runbooks for key rotation and cleanup, but do not rotate or clean without gate.
7. Document remaining risk.

Hard stop before deploy, function deletion, production cleanup, key rotation, migration apply, or auth/RLS/access-boundary changes.

---

# Antenor Command — Keyros Phase 3 Frontend

```text
START

Run in continuous autonomous work-queue mode.

Read:
1. ROADMAP.md
2. MASTER_STATE.md
3. ANTENOR_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. docs/knowledge/29-translation-architecture.md
8. Relevant frontend/module docs/knowledge/

Synchronize with latest main before continuing.

Victor approved Keyros Phase 3 Product Core.

Work only inside Antenor ownership: React UI, UX, frontend components, Dashboard UI, CRM UI, Contacts UI, Deals UI, Calendar UI, Messages UI, Billing UI presentation, responsive, accessibility, frontend performance and translations.

Draft PRs and reports are checkpoints, not stop conditions.

Continue automatically until a Hard Gate, ownership conflict, uncertainty, no eligible frontend work, or continuous work limit is reached.
```

## Antenor Phase 3 Queue

Mission: build the Product Core frontend progressively while keeping frontend/i18n/UX quality high.

Priority:

1. Verify and continue PR #31, or create a new clean frontend-only PR if #31 becomes contaminated.
2. Dashboard product polish.
3. CRM UI.
4. Contacts UI.
5. Deals UI.
6. Pipeline UI.
7. Calendar UI.
8. Activities UI.
9. Messages UX polish.
10. Billing UI consistency without Stripe/provider logic.
11. Finance UI shell/presentation only unless backend is ready.
12. Mobile UX.
13. Responsive improvements.
14. Accessibility.
15. Component consistency.
16. Performance optimization.
17. Frontend documentation.
18. Open/update draft PR.
19. Update engine/product report.
20. Continue automatically.

Antenor must not:

- touch backend
- touch Supabase
- touch Edge Functions
- touch migrations
- change RLS/auth/permission boundaries
- implement Billing Engine or Stripe logic
- mutate production data
- create fake backend behavior as if real

Rules:

- If backend support is missing, show safe empty/coming-soon/read-only states and report the backend blocker.
- All visible UI text must use translation IDs.
- Business logic must use stable IDs/codes, not translated labels.
- Do not translate enums, stage IDs, module keys, route keys or database values.

## Antenor Frontend Quality Queue

Continue quality work in parallel:

1. Remove remaining hardcoded visible strings.
2. Replace remaining `alert()`, `confirm()`, native prompts and unstyled messages.
3. Improve loading, empty, error, permission-denied, plan-locked and read-only states.
4. Improve modal/drawer accessibility.
5. Improve responsive layout.
6. Update docs only when behavior/rules changed.

Hard stop before backend work, Supabase changes, Edge Functions, migrations, production deploy, production mutation, auth/RLS/permission changes, Billing Engine/Stripe logic, destructive operation, or crossing Claude ownership.

---

# Native START Commands

Claude:

```text
START
```

Antenor:

```text
START
```
