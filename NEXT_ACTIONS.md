# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

## Current direction

The previous exceptional Claude Translation Architecture Audit is completed and merged through the approved PR flow.

Current parallel tracks:

- **Claude:** Repo ↔ Production Reconciliation before any future Edge Function/webhook/send deploy.
- **Antenor:** Frontend Quality & Internationalization, without entering Product Core yet.

Do not start Phase 3 Product Core until the repository/production drift is reconciled and frontend quality debt is reduced.

---

# Claude Track — Repo ↔ Production Reconciliation

## Objective

Align the repository with what is already live in production before any future deploy of webhook/send/resilience-related code.

## Focus

- Edge Functions
- rate limiter coverage
- circuit breaker code
- resilience migrations
- deployed-vs-repo drift
- docs/knowledge corrections
- report with exact drift map

## Hard rules

Claude must stop before:

- merge
- production deploy
- production data mutation
- migration apply
- auth/RLS change
- Billing Engine or Stripe work
- destructive operation

---

# Antenor Track — Frontend Quality & Internationalization

## Objective

Prepare the frontend for Phase 3 by reducing UI/i18n/design-system debt without depending on backend changes.

Antenor must not start Product Core features yet. This track is platform-quality work only.

## Ownership

Antenor owns:

- React UI
- UX consistency
- frontend components
- responsive behavior
- frontend i18n cleanup
- accessibility
- frontend performance
- frontend documentation

Antenor must not touch:

- Supabase migrations
- Edge Functions
- backend logic
- RLS
- auth/authorization boundaries
- Billing Engine
- Stripe/payment logic
- production data
- circuit breaker backend/shared implementation
- CLAUDE.md

## Required read order

1. `ROADMAP.md`
2. `MASTER_STATE.md`
3. `ANTENOR_ROLE.md`
4. `GATES.md`
5. `REPORT_FORMAT.md`
6. `docs/knowledge/29-translation-architecture.md`
7. Relevant frontend/module Knowledge Base docs

## Sprint structure

Antenor may continue automatically through frontend-only safe work until a Hard Gate or the continuous work limit is reached.

Each sprint must:

1. create or reuse a clean frontend-only branch
2. commit small changes
3. open or update a draft PR
4. create an `agent-room/reports/` report
5. continue to the next frontend-only sprint if no Hard Gate is reached

## Sprint 1 — i18n audit

1. Run or inspect the i18n audit script if available.
2. Identify visible hardcoded UI strings outside Dashboard.
3. Prioritize modules with visible user impact:
   - Messages
   - CRM
   - Contacts
   - Deals/Pipeline
   - Calendar
   - Team
   - Settings
   - Billing UI
4. Replace safe hardcoded strings with translation IDs.
5. Add missing keys in all supported locales already present in the repo.
6. Do not change business logic.
7. Do not translate internal IDs, enum values, stage IDs, module keys or route keys.

## Sprint 2 — Dashboard subcomponents

Audit and i18n-clean:

- WhatsAppAutomationStats
- LeadSourceChart
- RevenueExpenseChart
- ExpensesChart
- UpcomingAppointments
- RecentActivity
- PipelineChart

Rules:

- chart labels must come from translation IDs
- tooltips must come from translation IDs
- empty/loading/error text must come from translation IDs
- business calculations must continue using stable IDs/codes

## Sprint 3 — CRM/Contacts/Deals UI readiness

Audit frontend-only state quality:

- loading
- empty
- error
- permission denied
- plan locked
- read-only
- mobile layout
- drawer/modal labels
- buttons/actions

Fix safe UI-only issues.

Do not implement new Product Core data sync.

## Sprint 4 — Component consistency

Audit and consolidate frontend components where safe:

- Buttons
- Cards
- Inputs
- Tables
- Dialogs
- Drawers
- Badges
- Chips
- Empty States
- Skeletons
- Toasts/feedback labels

Avoid broad rewrites. Prefer small, reviewable component reuse.

## Sprint 5 — Accessibility and responsive pass

Audit and fix safe issues for:

- keyboard focus
- aria-labels
- contrast
- mobile overflow
- tablet layout
- desktop consistency
- drawer/modal navigation

## Sprint 6 — Frontend documentation

Update relevant docs only when behavior changes:

- `docs/knowledge/03-design-principles.md`
- `docs/knowledge/05-dashboard.md`
- `docs/knowledge/24-ux-rules.md`
- `docs/knowledge/29-translation-architecture.md`

## Acceptance criteria

Antenor's PR is acceptable when:

- it is frontend-only
- no backend/migration/auth/RLS/billing files are touched
- no user-facing text is newly hardcoded
- all new visible UI strings use translation IDs
- internal logic uses stable IDs/codes, not translated labels
- build/typecheck status is reported
- report lists remaining frontend/i18n debt

## Hard stop conditions

Stop immediately if the next action requires:

- backend changes
- Supabase changes
- migration
- production deploy
- auth/RLS/permission boundary change
- Billing Engine or Stripe logic
- destructive operation
- cross-agent conflict
- unclear ownership

## Report format addition

Antenor reports must include:

- modules audited
- translation keys added
- hardcoded strings removed
- files changed
- build/typecheck result
- remaining i18n debt
- remaining UX debt
- blockers
- next safe frontend sprint
