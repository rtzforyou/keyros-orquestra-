# NEXT

Status: ACTIVE
Date: 2026-07-08

Current state:
- PR 12 is merged.
- Team Real Model is complete for read-only production usage.
- Next gates: approve Module Registry v2, approve plan values, authorize Team Permissions Editing branch.

## Execution rule for both dev agents

Default mode is IMPLEMENTATION FIRST.

A task is not complete if it only produced an audit, plan, spec, or report.

For each task, the agent must either:

- change product code, create commit, update/open PR, run relevant checks, and report; or
- clearly mark BLOCKED with the exact gate or missing approval.

Audit and planning are allowed only as preparation for code changes, not as the final deliverable, unless a task explicitly says DESIGN ONLY.

## Claude — 20 implementation tasks

1. Implement Canonical Module Registry v2 as the backend source of truth in a branch/PR draft.
2. Add stable domain ids: dashboard, crm, calendar, messages, automations, team, billing, finance, reports, settings, agents.
3. Add legacy alias normalization for contacts/deals/pipeline -> crm, payments/expenses -> finance, ai/aiAgent -> agents.
4. Implement or draft backend helper to resolve organization entitlements from plan data.
5. Implement or draft backend helper to resolve member module access from role plus permissions.
6. Implement Team Permissions Editing backend slice as a branch/PR draft.
7. Add safe admin-only update flow for member permissions.
8. Add validation so non-admin users cannot edit protected member access fields.
9. Add audit events for member permission changes.
10. Add test fixtures for admin, member, invited user and cross-organization scenarios.
11. Implement plan tier constants or tables for Solo, Team, Business, Enterprise.
12. Implement default behavior for organization plan null so current production access is not broken.
13. Implement draft seat limit check for invitations without enabling paid provider integration.
14. Add tests for seat limit allowed and blocked cases.
15. Review and update affected RLS policies in a draft branch only.
16. Add rollback notes for every migration or policy change.
17. Implement backend report for current module permissions per organization.
18. Prepare Dashboard real-data resolver draft for CRM and finance metrics.
19. Remove or replace backend mock paths that block real dashboard data.
20. Produce PR-ready backend slice list with code commits, not only documentation.

Claude must stop before applying migrations, changing production data, merging main, deploying production, or implementing Stripe.

## Antenor — 20 implementation tasks

1. Fix production Team UI issues found after PR 12 merge and update/open a PR.
2. Implement editable Team Permissions UI draft based on Module Registry v2.
3. Implement permission checkbox/group components for member editing.
4. Implement read-only vs editable permission states for admin and member views.
5. Implement seat usage UI on Team invite flow.
6. Implement invite blocked-by-seat-limit UI state without backend provider integration.
7. Implement current plan card in Settings.
8. Implement plan comparison UI draft for Solo, Team, Business, Enterprise.
9. Implement module locked-state UI for unavailable domains.
10. Implement upgrade CTA UI states without external provider integration.
11. Replace remaining frontend MockData usages where real hooks already exist.
12. Fix risky hardcoded module ids, plan names, permission labels and duplicated strings.
13. Refactor reusable UI patterns for buttons, cards, badges, tables, drawers, forms and alerts where duplication exists.
14. Improve Settings UX across account, studio, team and plan sections with real components.
15. Improve CRM UX across contacts, deals, pipeline and drawers with code changes.
16. Improve Dashboard UX loading, empty and error states for real-data readiness.
17. Improve responsive layout for Team, Settings, CRM and Dashboard.
18. Fix console errors, obvious warnings and blank-screen risks discovered during smoke checks.
19. Add or update frontend smoke checklist file tied to the implemented changes.
20. Produce PR-ready frontend slice list with code commits, not only documentation.

Antenor must stop before main merge, deploy, backend ownership changes, or business-rule changes.

Output short status only with branch, commit, PR, report path, blockers and next step.
