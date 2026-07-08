# NEXT

Status: ACTIVE
Date: 2026-07-08

Current state:
- PR 12 is merged.
- Team Real Model read-only is live in production.
- Module Registry v2 is approved as architecture.
- PR 13 exists as draft for Team Permissions Editing.
- Critical finding: invitations policy allows indirect privilege escalation. This must be fixed before Team Editing goes live.

## Execution rule for both dev agents

Default mode is IMPLEMENTATION FIRST.

A task is not complete if it only produced an audit, plan, spec, or report.

For each task, the agent must either:
- change product code, create commit, update/open PR, run relevant checks, and report; or
- clearly mark BLOCKED with the exact gate or missing approval.

Audit and planning are preparation for code changes, not final deliverables, unless a task explicitly says DESIGN ONLY.

## Gate decision

Approved:
- Module Registry v2 architecture.
- Start Slice 1 as PR draft work.
- Include invitations security fix in Slice 1.

Not approved yet:
- Merge PR 13.
- Apply migrations.
- Production data changes.
- Stripe implementation.

## Claude — next 20 implementation tasks

1. Update PR 13 or create a follow-up PR to include the invitations security fix.
2. Make invitation INSERT admin-only inside the same organization.
3. Make invitation UPDATE admin-only inside the same organization.
4. Make invitation DELETE or revoke admin-only inside the same organization.
5. Add tests proving members cannot invite admins.
6. Add tests proving admins can invite valid members.
7. Add tests proving cross-organization invite manipulation is blocked.
8. Finish Team Permissions Editing backend slice in PR draft form.
9. Implement last-admin protection if not already complete.
10. Ensure audit logging works after the required migration is applied.
11. Keep Module Registry v2 as TS constants unless a database table becomes necessary.
12. Normalize legacy permissions safely and idempotently.
13. Confirm plan_id NULL keeps current behavior unchanged.
14. Implement get_org_entitlements draft without enabling billing provider logic.
15. Implement member effective access resolver draft.
16. Prepare exact migration apply order for Slice 1.
17. Prepare rollback notes for each migration in Slice 1.
18. Run build/typecheck and targeted tests for changed files.
19. Publish short report with branch, commit SHA, PR, migrations, tests and risks.
20. Stop at merge/apply gate and request Victor/ChatGPT approval.

## Antenor — next 20 implementation tasks

1. Rebase Billing UX draft on latest main after PR 12 merge.
2. Update frontend module labels to match Module Registry v2 domains.
3. Replace ai labels with Agents where relevant.
4. Replace payments/expenses UI language with Billing/Finance where relevant.
5. Implement Team Permissions Editing UI against PR 13 draft API shape.
6. Add admin-only edit access action in Team UI if not already complete.
7. Add non-admin read-only Team state.
8. Add invite blocked-state UI for member users.
9. Add warning copy for admin-only invitation actions.
10. Implement current plan and seats cards as UI-only components.
11. Implement locked domain state for modules not included in plan.
12. Replace hardcoded legacy module ids in frontend code.
13. Improve Settings layout for Team, Plan and Account sections.
14. Improve CRM loading, empty and error states with code changes.
15. Improve Dashboard loading, empty and error states with code changes.
16. Improve mobile layout for Team, Settings, CRM and Dashboard.
17. Fix console errors and obvious warnings found during smoke checks.
18. Run build/typecheck for frontend changes.
19. Publish short report with branch, commit SHA, PR, checks and risks.
20. Stop at merge/deploy gate and request Victor/ChatGPT approval.

Output short status only with branch, commit, PR, report path, blockers and next step.
