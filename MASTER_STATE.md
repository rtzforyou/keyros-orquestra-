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
- Current gate: approve Module Registry v2 and authorize next implementation slices.

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
