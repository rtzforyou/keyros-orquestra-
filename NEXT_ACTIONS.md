# NEXT ACTIONS

Date: 2026-07-08
Freshness rule: this file wins over older chat context when there is conflict.

## Global direction

Current priority: move from Knowledge Base v1 into Phase 2 — Platform Consolidation.

The Knowledge Base v1 has been produced in Product PR #19 and is approved by Victor/ChatGPT for merge after normal repo gate handling.

Next work must consolidate the platform before expanding new product modules.

## Current gates

Agents must stop before:

- merge to `main`
- production deploy
- production data change
- database migration apply
- auth/access boundary change
- payment provider implementation
- destructive operation
- cross-agent conflict
- major architecture decision

## Active EPIC 2 — Platform Consolidation

### Objective

Make Keyros stable as a platform layer before adding new modules.

Focus areas:

- Module Registry consistency
- permissions and entitlements checks
- frontend/backend vocabulary alignment
- mock/fallback data visibility
- tenant isolation boundaries
- documentation status hygiene

This EPIC must not introduce Stripe provider code, destructive migrations, production deploys or auth boundary changes without gate approval.

## Claude queue — backend/platform consolidation

Maximum: 15–20 tasks per loop.

1. Read `ROADMAP.md`, `MASTER_STATE.md`, `GATES.md`, `CLAUDE_ROLE.md`, and the merged/active `docs/knowledge/` docs before changing code.
2. Audit current Module Registry definitions and confirm canonical module keys match approved bounded contexts:
   - `dashboard`
   - `crm`
   - `calendar`
   - `messages`
   - `automations`
   - `team`
   - `billing`
   - `finance`
   - `reports`
   - `settings`
   - `agents`
3. Search backend and shared code for legacy aliases that must normalize to approved domains:
   - contacts/deals/pipeline -> `crm`
   - payments/expenses -> `finance`
   - ai/aiAgent -> `agents`
4. Verify permission checks use Module Registry keys instead of screen-specific or hardcoded aliases where possible.
5. Verify entitlement checks are centralized or clearly routed through the approved registry/billing boundary.
6. Verify admin-only areas remain protected:
   - team invitations
   - billing
   - module/permission management
   - sensitive settings
7. Review RLS-sensitive flows touched by permissions or entitlements.
8. Confirm no frontend-only permission state can bypass backend/RLS protection.
9. Review audit coverage for permission, invitation, billing/entitlement, and admin-sensitive actions.
10. Review rate limit coverage for public or abuse-prone Edge Functions.
11. Review idempotency/retry/circuit-breaker status for webhook and integration paths; document gaps as `Planned` if not implemented.
12. Add or update tests where safe for registry normalization, permission guard behavior, and entitlement checks.
13. Update Knowledge Base docs if implementation differs from documentation.
14. Produce a report in `agent-room/reports/` with branch, commit SHA, PR, tests run, findings, blockers and next recommended EPIC.
15. Open a draft PR if code or docs changed.
16. Stop before merge, production deploy, production data change, migration apply, auth boundary change, or payment provider implementation.

## Antenor queue — frontend/platform consolidation

Maximum: 10 tasks per cycle.
Implementation First: fix safe frontend-only issues found during the audit.

1. Read `ROADMAP.md`, `NEXT_ACTIONS.md`, `ANTENOR_ROLE.md`, and the active `docs/knowledge/` docs for UI-owned modules.
2. Audit frontend module navigation and confirm visible modules match canonical keys from Module Registry.
3. Identify UI places still using legacy labels as architecture keys instead of display labels:
   - contacts/deals/pipeline
   - payments/expenses
   - ai/aiAgent
4. Verify read-only, permission-denied and plan-locked states are clear and consistent across Team, Billing UI, Dashboard, CRM and Messages.
5. Verify Settings and Team UI never imply the user can perform admin-only actions when permissions are missing.
6. Check Dashboard for mock/fallback values and make them explicit if still present.
7. Check CRM, Contacts and Deals UI for safe loading, empty and error states.
8. Check Billing UI for placeholder/coming-soon states; do not implement Stripe or provider logic.
9. Fix safe frontend-only issues discovered during the cycle.
10. Produce a report with current loop area, branch, commit SHA, PR if code changed, report path, blockers and next loop area.

Antenor must not change backend rules, database schema, RLS, billing provider logic or auth/access boundaries.

## ChatGPT responsibilities

1. Review PR #19 final state and confirm Knowledge Base merge gate.
2. Review Platform Consolidation PRs before merge.
3. Approve or reject gates.
4. Keep architecture consistent with `ROADMAP.md`, `MASTER_STATE.md`, `GATES.md`, role files and Knowledge Base.
5. Ensure Claude and Antenor remain separated by ownership boundaries.

## Success condition for this cycle

This cycle is successful when:

- Knowledge Base v1 is merged or explicitly queued for merge by Victor/ChatGPT.
- Module Registry and permission boundaries are audited.
- Legacy aliases are either normalized or documented as planned cleanup.
- UI permission/plan states are clear and safe.
- No production deploy, migration, auth boundary change or payment provider implementation happens without gate approval.
