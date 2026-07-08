# KEYROS ROADMAP

Date: 2026-07-08
Owner: ChatGPT Architecture Review
Repository role: Orchestration source of truth

This roadmap defines the direction after the current WhatsApp identity and Knowledge Base stabilization work.

## Current strategic rule

Do not expand new product modules before the Knowledge Base exists.

The Keyros system must first become understandable by humans and agents through official documentation. After that, implementation can continue with less context loss, fewer contradictions and safer cross-agent work.

## Phase 0 — Current gates and stabilization

Goal: finish the currently open identity and coordination work before moving to new platform layers.

Required outcomes:

- Validate and close PR #17 for group identity.
- Ensure group conversations use `group.subject` as identity.
- Ensure direct conversations keep the corrected interlocutor identity.
- Confirm no regression for `DIRECT`, `GROUP`, `CHANNEL`, and `UNKNOWN` conversation types.
- Keep all production deploys, merges and migrations behind gate approval.

Exit criteria:

- All active PRs are reviewed.
- Identity rules are documented.
- No known blocker remains in WhatsApp identity.

## Phase 1 — Keyros Knowledge Base

Goal: create the official documentation brain inside the product repository before creating a standalone `keyros-knowledge` repository.

Required outcomes:

- Create `docs/knowledge/` in the product repository.
- Document product philosophy, architecture, CRM, dashboard, contacts, deals, messages, WhatsApp, calendar, team, billing, module registry, automations, agents, security, database, API, edge functions, testing, runbooks, playbooks, business rules, UX rules, troubleshooting, known limitations, roadmap and glossary.
- Each document must explain functionality, architecture, rules, examples, flows, decisions, trade-offs and how an AI should interpret that module.
- Align Engine and Orquestra with the Knowledge Base.

Exit criteria:

- Agents can start from documentation instead of previous chat context.
- The Knowledge Base describes the current system without pretending that planned features already exist.
- The documentation is good enough to become the seed of the future `keyros-knowledge` organization/repository.

## Phase 2 — Platform Consolidation

Goal: make Keyros stable as a platform before increasing product complexity.

Required outcomes:

- Repository/service boundaries clarified.
- Module Registry used consistently by backend and frontend.
- Entitlements and permissions enforced across relevant modules.
- Centralized configuration documented.
- Event/automation boundaries clarified.
- RLS, rate limiting, idempotency, retries, audit and safety rules reviewed.

Exit criteria:

- New modules can be added without reinventing permissions, access, billing or registry logic.
- Backend and frontend share the same module vocabulary.

## Phase 3 — Product Core

Goal: make the core CRM product real, useful and measurable.

Priority modules:

1. CRM
   - Contacts
   - Deals
   - Pipeline
   - Activities
   - Relationship between conversations and deals

2. Dashboard and Finance
   - Real opportunity value from deals
   - Gross revenue
   - Net profit logic
   - Expenses under `finance`
   - MRR/ARR only when billing data is real

3. Messages
   - WhatsApp continuity
   - Conversation states
   - Safe loading/error/empty states
   - Identity protection maintained

4. Calendar
   - Appointments
   - CRM linkage
   - Future automation triggers

Exit criteria:

- Dashboard uses real data or explicitly labeled placeholders.
- Deals can drive financial understanding.
- Core CRM flows can be used by a real business without mock-only behavior.

## Phase 4 — Billing Engine

Goal: make monetization safe and centralized.

Rules:

- Stripe belongs only inside the Billing Engine.
- No payment provider implementation without gate approval.
- Billing must control plans, seats, subscriptions, invoices, module access and entitlements.

Required outcomes:

- Plans and seat limits.
- Subscription states.
- Entitlement enforcement.
- Admin-only billing management.
- Stripe webhook architecture documented before implementation.

Exit criteria:

- Billing can safely decide what an organization can access.
- Product UI never implements payment logic directly.

## Phase 5 — Automations Engine

Goal: create safe business automation after CRM, billing and knowledge rules are stable.

Required outcomes:

- Triggers.
- Conditions.
- Delays.
- Actions.
- Webhooks.
- Audit trail.
- Failure handling.
- Rate limiting.
- Human-readable automation reports.

Exit criteria:

- Automations can act on CRM, messages, calendar and finance without breaking tenant isolation or identity rules.

## Phase 6 — Agents Platform

Goal: introduce AI agents as operational workers, not decorative chatbots.

Potential agents:

- Sales Agent
- CRM Agent
- Finance Agent
- Support Agent
- Calendar Agent
- Marketing Agent
- Analytics Agent

Rules:

- Agents must read the Knowledge Base first.
- Agents must respect gates.
- Agents must never mutate production without authorization.
- Agents must report branch, commit, PR, report path and blockers.

Exit criteria:

- Agents can safely perform scoped work with traceability and without relying on hidden chat history.

## Phase 7 — Ecosystem and Scale

Goal: transform Keyros from CRM into a service-business operating system.

Potential outcomes:

- Public API.
- SDK.
- Chrome extension.
- Marketplace of modules.
- Marketplace of automations.
- Marketplace of agents.
- Partner integrations.
- Standalone `keyros-knowledge` repository or organization.

Exit criteria:

- External developers and agents can extend Keyros without compromising architecture, billing or security.

## Permanent priorities

1. Security before features.
2. Architecture before speed.
3. Documentation before expansion.
4. Real data before dashboards.
5. Billing Engine before Stripe usage.
6. Knowledge Base before autonomous agents.
7. Gates before merge, deploy, migration or production data changes.
