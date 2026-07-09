# MASTER STATE

Date: 2026-07-09

## Repositories

- Product: `rtzforyou/easytattoo-crm`
- Engine: `rtzforyou/keyros-engine`
- Orchestration: `rtzforyou/keyros-orquestra-`

## Current product state

- PR #9: merged.
- PR #12: merged.
- PR #13: merged — Team Permissions, Module Registry, Admin Invitations, Entitlements.
- PR #15: merged — Messages UI, Safe States, ConversationType, deduplication by `remote_jid`.
- PR #16: merged — WhatsApp private conversation identity fix.
- PR #17: merged — group identity repair and migration file reconciled with production.
- PR #19: merged — Keyros Knowledge Base v1.
- PR #25: merged — invitation audit migration file.
- PR #25 migration: applied in production and tested with rollback; invitation audit gap closed.
- PR #27: merged — Dashboard i18n / Translation Architecture.
- PR #28: merged — build reconciliation for missing `useWhatsAppAvailability` and orphan `ContactFormsTab` import.
- PR #26: remains blocked and must be rebased before review.
- PR #29: draft/open — Repo ↔ Production Reconciliation for backend resilience artifacts.

## Current production state

- Main builds clean after PR #28.
- Cloudflare Pages production deploy was reported successful after approved merges.
- Dashboard i18n fix is live after PR #27.
- Invitation audit triggers are installed in production and tested.
- Repo ↔ production drift for backend resilience is being reconciled in PR #29.

## Current operating model

Keyros Orquestra uses roadmap-driven autonomous mode with Hard Gates and Autonomous Checkpoints.

Agents should not depend on Victor/ChatGPT manually writing every next task when the roadmap, master state and next-action file are clear.

Default execution model:

1. Read `ROADMAP.md`.
2. Read `MASTER_STATE.md`.
3. Read own role file.
4. Read `GATES.md`.
5. Read `REPORT_FORMAT.md`.
6. Read `NEXT_ACTIONS.md` when it contains a fresher override.
7. Read relevant `docs/knowledge/` docs.
8. Select the next eligible work item inside the agent ownership boundary.
9. Work on a branch, never on `main`.
10. Commit in small steps.
11. Open or update a draft PR as a checkpoint.
12. Write or update an `agent-room/reports/` report as a checkpoint.
13. Check Hard Gates.
14. If no Hard Gate is crossed, continue automatically to the next eligible work.

`NEXT_ACTIONS.md` is optional in normal execution, but wins when it contains a fresher explicit override from Victor/ChatGPT.

## Current gate posture

Agents are approved to START automatically using `ROADMAP.md` + `MASTER_STATE.md` + `GATES.md` + fresh `NEXT_ACTIONS.md`.

Agents must stop only for Hard Gates, uncertainty, ownership conflicts, blocked EPICs, no remaining eligible work, or the continuous work limit.

They are not approved to:

- merge to `main`
- deploy production
- apply migrations
- mutate production data
- change auth/RLS/tenant isolation boundaries
- implement payment provider logic
- cross ownership boundaries
- skip roadmap order
- start blocked EPICs

They are approved to continue automatically when:

- work stays inside the same agent ownership boundary
- work is isolated in branch + draft PR
- a report is written or updated
- no Hard Gate is crossed
- no migration/deploy/production/auth/RLS/billing/destructive action is required

## Checkpoint rule

A draft PR is not a stopping condition.

A report is not a stopping condition.

A completed sprint is not a stopping condition.

Agents must checkpoint early and continue automatically when safe.

Recommended checkpoint triggers:

- one coherent batch completed
- more than 10 files changed
- more than 5 commits accumulated
- one module completed
- before switching domain/module
- before doing work that might become harder to review later

## Continuous work limit

Each agent may continue automatically until the first of these happens:

- a Hard Gate is reached
- 3 consecutive EPICs/major loops are completed without human review
- 24 hours of continuous work has passed
- the agent is uncertain whether the next step is safe
- there is no remaining eligible work inside its ownership boundary

After that, the agent must stop and report.

## Approved architecture

Canonical Module Registry is approved as a Keyros architecture layer.

The registry is the source of truth for:

- Team member permissions
- Plan capabilities
- Product access
- Feature flags
- Future module access

## Approved domain model v2

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

## Architecture decisions

- Keep `crm` as a domain. Do not remove it.
- `contacts`, `deals`, pipeline and activities belong under `crm`.
- Use `finance` instead of a standalone `expenses` domain.
- Use `billing` for plans, seats, subscriptions, invoices and provider integration.
- Use `agents` instead of generic `ai`.
- Model Keyros by bounded contexts, not only by screens.
- `billing` is core and admin-only.
- `reports` is reserved until real reporting rules are defined.
- Legacy aliases must be normalized: contacts/deals/pipeline -> crm, payments/expenses -> finance, ai/aiAgent -> agents.

## Current known findings

The following findings should guide the next eligible backend/frontend work, while respecting Hard Gates:

1. Backend resilience repo↔production drift is being reconciled in PR #29. Do not deploy webhook/send/resilience code from main until this is merged and verified.
2. PR #26 remains blocked and must be rebased onto the latest main before review.
3. Frontend i18n debt remains outside Dashboard, especially charts, Messages, CRM, Contacts, Deals/Pipeline, Calendar, Team, Settings and Billing UI.
4. Frontend quality debt remains: loading, empty, error, permission-denied, plan-locked, responsive and accessibility states.
5. Entitlements exist as planned/incomplete wiring and belong to future Billing Engine work.
6. Phase 3 Product Core should not start until repo↔production reconciliation and frontend platform-quality debt are reduced.