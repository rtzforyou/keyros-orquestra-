# MASTER STATE

Date: 2026-07-08

## Repositories

- Product: `rtzforyou/easytattoo-crm`
- Engine: `rtzforyou/keyros-engine`
- Orchestration: `rtzforyou/keyros-orquestra-`

## Current product state

- PR #9: merged.
- PR #9 merge commit: `541ea2c7f79da85e4c3d35f83f613c173bdeb569`
- Team migration: applied in production.
- Remote migration record reported by Claude: `20260708144838`
- PR #12: merged.
- PR #12 merge commit reported by Claude: `f3cd200`
- Cloudflare production deploy: success reported by Claude.
- Team page in production: real read-only members.
- PR #13: merged — Team Permissions, Module Registry, Admin Invitations, Entitlements.
- PR #15: merged — Messages UI, Safe States, ConversationType, deduplication by `remote_jid`.
- PR #16: merged — WhatsApp private conversation identity fix.
- PR #17: group identity work reached gate/review flow.
- PR #19: Keyros Knowledge Base v1 produced and approved by Victor/ChatGPT for merge handling through normal gate flow.
- EPIC 2 Platform Consolidation audit completed by Claude; safe docs corrections applied; backend/prod mutations remain gated findings.
- Antenor completed multiple frontend consolidation loops; next frontend work should be selected from ROADMAP + MASTER_STATE, not from manual ad-hoc queues.

## Current operating model

Keyros Orquestra now uses roadmap-driven autonomous mode.

Agents should no longer depend on Victor/ChatGPT manually writing every next task when the ROADMAP and MASTER_STATE are clear.

Default execution model:

1. Read `ROADMAP.md`.
2. Read `MASTER_STATE.md`.
3. Read own role file.
4. Read `GATES.md`.
5. Read `REPORT_FORMAT.md`.
6. Read relevant `docs/knowledge/` docs.
7. Select the next eligible EPIC inside the agent ownership boundary.
8. Work on a new branch.
9. Commit in small steps.
10. Open a draft PR.
11. Write an `agent-room/reports/` report.
12. Stop at gate.

`NEXT_ACTIONS.md` is now optional. It is only for temporary explicit overrides from Victor/ChatGPT.

## Current gate posture

Agents are approved to START automatically using `ROADMAP.md` + `MASTER_STATE.md` + `GATES.md`.

They are not approved to:

- merge to `main`
- deploy production
- apply migrations
- mutate production data
- change auth/RLS/tenant isolation boundaries
- implement payment provider logic
- cross ownership boundaries
- skip roadmap order

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

## Current known findings from Platform Consolidation

The following findings should guide the next eligible backend/frontend work, while respecting gates:

1. Rate limit is absent in `whatsapp-send`, `automation-bulk-send`, and `automation-campaign-control` despite config existing. This is high priority before scale.
2. Invitation create/revoke/resend flows need audit logging.
3. Circuit breaker is planned, not implemented; docs must not claim it exists until implemented.
4. Frontend vocabulary still has legacy drift in some areas and should align with Module Registry terminology.
5. Entitlements exist as planned/incomplete wiring and belong to future Billing Engine work.
6. Any migration already applied in production but missing from `main` must be reconciled through gate review.
