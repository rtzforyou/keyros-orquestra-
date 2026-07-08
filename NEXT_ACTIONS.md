# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

## Exceptional override — Claude Translation Architecture Audit

This is an explicit Victor/ChatGPT override for the next Claude execution.

Reason:

The dashboard currently shows mixed languages: sidebar translations follow the selected locale, while dashboard metric labels and some module texts remain hardcoded in Portuguese. This indicates an i18n architecture drift: some UI texts are using translation IDs, while others are embedded directly in React/components/hooks.

This task is exceptional because Claude is allowed to touch frontend/i18n files normally owned by Antenor. The exception is valid only for this execution and only for translation architecture cleanup.

After this task, normal ownership returns:

- Claude: backend/platform/architecture/security
- Antenor: frontend/UX/components

## Objective

Create a safe, consistent translation architecture pass so Keyros UI text is driven by IDs/namespaces instead of hardcoded strings.

The immediate visible bug to fix:

- Dashboard sidebar is in French, but Dashboard cards remain in Portuguese.

The architectural rule to enforce:

- Business logic uses stable IDs/codes.
- UI displays translated labels through translation files.
- Components never hardcode user-facing text unless it is a temporary diagnostic string explicitly marked as such.

## Claude allowed scope for this exceptional task

Claude may edit:

- translation/i18n files
- Dashboard frontend files
- shared UI label helpers
- TypeScript types related to translation keys if needed
- docs/knowledge files related to i18n, UX rules, dashboard, glossary or architecture
- lint/test scripts for detecting hardcoded UI text if safe
- agent-room report

Claude must not edit:

- Supabase migrations
- production data
- RLS
- authentication
- authorization boundaries
- Billing Engine
- Stripe/payment provider logic
- unrelated backend features
- unrelated UI redesigns

## Required tasks

1. Read `ROADMAP.md`, `MASTER_STATE.md`, `GATES.md`, `CLAUDE_ROLE.md`, `docs/knowledge/`, and current translation/i18n implementation.
2. Identify the active i18n system and where translations are stored.
3. Audit Dashboard for hardcoded user-facing text.
4. Replace dashboard metric/card labels with translation IDs.
5. Add missing translations for at least the active supported locales already present in the repository.
6. Use namespaced keys such as:
   - `dashboard.metrics.openOpportunities`
   - `dashboard.metrics.newLeads`
   - `dashboard.metrics.grossRevenue`
   - `dashboard.metrics.expenses`
   - `dashboard.metrics.netProfit`
   - `dashboard.metrics.wonDeals`
   - `dashboard.metrics.conversionRate`
   - `dashboard.metrics.averageTicket`
   - `dashboard.pipelineFunnel.title`
   - `dashboard.pipelineFunnel.subtitle`
7. Audit visible Dashboard period filters and chart labels for hardcoded text.
8. Ensure internal logic does not depend on translated labels.
9. Ensure stage/status/category logic uses IDs/codes, not visible strings.
10. Add or update a documentation file explaining Keyros Translation Architecture.
11. Document the rule: UI text = translation ID; business logic = stable ID/code.
12. Add a lightweight audit note or script if safe to help find remaining hardcoded UI strings later.
13. Do not attempt a full-app translation refactor in one PR unless it is small and safe.
14. Produce a report in `agent-room/reports/` with:
    - files changed
    - translation keys added
    - hardcoded strings removed
    - remaining i18n debt
    - screenshots/manual test instructions
    - blockers
15. Open a draft PR.
16. Stop before merge or production deploy.

## Acceptance criteria

The PR is acceptable when:

- Dashboard labels follow the selected language consistently.
- The sidebar and dashboard no longer mix French and Portuguese for core dashboard UI.
- New keys are namespaced and reusable.
- No business logic depends on translated text.
- No backend/migration/auth/billing changes are introduced.
- Documentation explains the translation architecture rule.

## Hard stop conditions

Stop immediately if the work requires:

- migration
- production data change
- auth/RLS change
- billing provider implementation
- backend behavior change unrelated to i18n
- large cross-app refactor that cannot be reviewed safely

## Previous Platform Consolidation context

The following findings still exist but are not the focus of this exceptional task:

1. Rate limit is absent in `whatsapp-send`, `automation-bulk-send`, and `automation-campaign-control` despite config existing.
2. Invitation create/revoke/resend flows need audit logging.
3. Circuit breaker is planned, not implemented.
4. Frontend vocabulary drift exists in some areas and should align with Module Registry terminology.
5. Entitlements exist as planned/incomplete wiring and belong to future Billing Engine work.
6. Any migration already applied in production but missing from `main` must be reconciled through gate review.
