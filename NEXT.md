# NEXT

Status: ACTIVE
Date: 2026-07-08

Current state:
- PR 12 is merged.
- Team Real Model is complete for read-only production usage.
- Next gates: approve Module Registry v2, approve plan values, authorize Team Permissions Editing branch.

## Claude — 10 large tasks

1. Finalize Canonical Module Registry v2 as source of truth.
2. Normalize legacy module aliases in existing data and reports.
3. Prepare Team Permissions Editing branch and PR draft.
4. Design database changes for editable member permissions.
5. Design safe admin update flow for team member permissions.
6. Design plan keys, plan modules and team seat limits.
7. Prepare database-side invite seat limit plan.
8. Prepare Billing Entitlements architecture without Stripe implementation.
9. Prepare Dashboard real-data backend plan for finance and CRM metrics.
10. Prepare security review plan for Team, modules, plans and access checks.

Claude must stop before migrations, production data changes, main merge, deploy, or Stripe implementation.

## Antenor — 10 large tasks

1. Audit production Team UI after PR 12 merge.
2. Prepare editable Team Permissions UX based on Module Registry v2.
3. Prepare admin-only Billing and plan UX screens.
4. Expand frontend plan and seats UX states.
5. Continue MockData cleanup with ownership map.
6. Harden Settings UX across account, studio, team and plan sections.
7. Harden CRM UX across contacts, deals, pipeline and drawers.
8. Harden Dashboard UX for real-data loading, empty and error states.
9. Clean reusable design system patterns: buttons, cards, tables, drawers, forms and alerts.
10. Maintain QA, mobile and performance checklist across the main app.

Antenor must stop before main merge, deploy, backend ownership changes, or business-rule changes.

Output short status only with branch, commit, PR, report path, blockers and next step.
