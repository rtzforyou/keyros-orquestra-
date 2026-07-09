# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

## Current direction

Current parallel tracks:

- **Claude:** Repo ↔ Production Reconciliation before any future Edge Function/webhook/send deploy.
- **Antenor:** Frontend Quality & Internationalization, without entering Product Core yet.

Do not start Phase 3 Product Core until the repository/production drift is reconciled and frontend quality debt is reduced.

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

# Claude Work Queue — Repo ↔ Production Reconciliation

## Objective

Align the repository with what is already live in production before any future deploy of webhook/send/resilience-related code.

## Current known state

Claude produced Product PR #29 for backend resilience repo↔production reconciliation.

PR #29 should be reviewed through the normal gate flow. Claude must not merge it himself.

## Queue

1. Keep PR #29 updated if review reveals safe fixes.
2. Verify recovered backend resilience artifacts remain aligned with deployed production source.
3. Verify `whatsapp-send`, `automation-bulk-send`, `automation-campaign-control`, `_shared/circuitBreaker.ts`, `_shared/rateLimiter.ts` and related migration files are in repo state.
4. Verify no frontend-only WIP was accidentally included.
5. Update docs/knowledge only if implementation differs.
6. Produce/update engine report with exact drift map.
7. After PR #29 is merged by Victor/ChatGPT, select the next backend/platform eligible work from `ROADMAP.md` and `MASTER_STATE.md`.

## Hard rules

Claude must stop before merge, production deploy, production data mutation, migration apply, auth/RLS change, Billing Engine or Stripe work, destructive operation, or crossing ownership boundaries.

---

# Antenor Work Queue — Frontend Quality & Internationalization

## Objective

Prepare the frontend for Phase 3 by reducing UI/i18n/design-system debt without depending on backend changes.

Antenor must not start Product Core features yet. This track is platform-quality work only.

## Ownership

Antenor owns React UI, UX consistency, frontend components, responsive behavior, frontend i18n cleanup, accessibility, frontend performance and frontend documentation.

Antenor must not touch Supabase migrations, Edge Functions, backend logic, RLS, auth boundaries, Billing Engine, Stripe/payment logic, production data, circuit breaker backend/shared implementation or `CLAUDE.md`.

## Required read order

1. `ROADMAP.md`
2. `MASTER_STATE.md`
3. `ANTENOR_ROLE.md`
4. `GATES.md`
5. `REPORT_FORMAT.md`
6. `docs/knowledge/29-translation-architecture.md`
7. Relevant frontend/module Knowledge Base docs

## Autonomous queue rule

Antenor must keep moving through the queue until a Hard Gate or continuous work limit is reached.

Antenor must checkpoint frequently:

1. create or reuse a clean frontend-only branch
2. commit small changes
3. open or update a draft PR
4. create or update an `agent-room/reports/` report
5. continue to the next frontend-only eligible item if no Hard Gate is reached

## Queue Area 1 — i18n audit

1. Run or inspect the i18n audit script if available.
2. Identify visible hardcoded UI strings outside Dashboard.
3. Prioritize modules with visible user impact: Messages, CRM, Contacts, Deals/Pipeline, Calendar, Team, Settings, Billing UI.
4. Replace safe hardcoded strings with translation IDs.
5. Add missing keys in all supported locales already present in the repo.
6. Do not change business logic.
7. Do not translate internal IDs, enum values, stage IDs, module keys or route keys.
8. Checkpoint with draft PR/report, then continue.

## Queue Area 2 — Dashboard subcomponents

Audit and i18n-clean WhatsAppAutomationStats, LeadSourceChart, RevenueExpenseChart, ExpensesChart, UpcomingAppointments, RecentActivity and PipelineChart.

Rules: chart labels, tooltips, empty/loading/error text must come from translation IDs; business calculations must continue using stable IDs/codes; checkpoint with draft PR/report, then continue.

## Queue Area 3 — CRM/Contacts/Deals UI readiness

Audit and fix safe frontend-only state quality: loading, empty, error, permission denied, plan locked, read-only, mobile layout, drawer/modal labels and buttons/actions.

Do not implement new Product Core data sync.

Checkpoint with draft PR/report, then continue.

## Queue Area 4 — Component consistency

Audit and consolidate safe component reuse for Buttons, Cards, Inputs, Tables, Dialogs, Drawers, Badges, Chips, Empty States, Skeletons and Toasts/feedback labels.

Avoid broad rewrites. Prefer small, reviewable component reuse.

Checkpoint with draft PR/report, then continue.

## Queue Area 5 — Accessibility and responsive pass

Audit and fix safe issues for keyboard focus, aria-labels, contrast, mobile overflow, tablet layout, desktop consistency and drawer/modal navigation.

Checkpoint with draft PR/report, then continue.

## Queue Area 6 — Frontend documentation

Update relevant docs only when behavior changes: `docs/knowledge/03-design-principles.md`, `docs/knowledge/05-dashboard.md`, `docs/knowledge/24-ux-rules.md`, `docs/knowledge/29-translation-architecture.md`.

Checkpoint with draft PR/report, then continue to the next eligible frontend platform-quality item.

## PR review criteria

Antenor's PR is acceptable when it is frontend-only, no backend/migration/auth/RLS/billing files are touched, no user-facing text is newly hardcoded, all new visible UI strings use translation IDs, internal logic uses stable IDs/codes, build/typecheck status is reported, and remaining frontend/i18n debt is listed.

These criteria are for review. They are not a reason for Antenor to stop early if more safe frontend work remains.

## Hard stop conditions

Stop immediately if the next action requires backend changes, Supabase changes, migration, production deploy, auth/RLS/permission boundary change, Billing Engine or Stripe logic, destructive operation, cross-agent conflict or unclear ownership.

## Report format addition

Antenor reports must include modules audited, translation keys added, hardcoded strings removed, files changed, build/typecheck result, remaining i18n debt, remaining UX debt, blockers and next safe frontend work item.
