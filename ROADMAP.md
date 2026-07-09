# KEYROS ROADMAP

Date: 2026-07-09
Owner: ChatGPT Architecture Review
Repository role: Orchestration source of truth

This roadmap defines the current Keyros execution direction after backend resilience reconciliation, shared-layer adoption for retry/scheduler, EPIC 3 frontend consistency completion, and the rich shared template renderer merge.

## Current strategic rule

Do not expand new product modules before the Product Core is stable and readable by agents.

The Keyros system must remain understandable by humans and agents through official documentation, reports and clear gates. Implementation can continue only when context loss, contradictions and cross-agent drift are controlled.

## Current execution state — 2026-07-09

### Completed / merged

- Backend repo/prod drift reconciliation: PR #36 merged.
- Shared layer additive foundation: PR #37 merged.
- `automation-retry` shared template adoption: PR #42 merged and deployed as v8.
- `automation-scheduler` shared template adoption: PR #43 merged and deployed as v24.
- EPIC 3 — Frontend Consistency & Module Registry Alignment: PR #31 merged.
- Shared rich template renderer, ADR-0003 Option C: PR #44 merged.

### Completed EPIC 3 details

EPIC: EPIC 3 — Frontend Consistency & Module Registry Alignment
Status: COMPLETED
Sprints executed: 1
Product branch: `antenor/frontend-i18n-consolidation`
Product merge commit: `94e7ad3ad5897209343d0b50ddfa0c4c58563287`
Original Antenor commit: `18a2be6ce8e30bca2d8293992b8d0d662e6cf4e5`
Product PR: #31
Report: `2026-07-09-antenor-i18n-consolidation.md`
Blocks: None

### Current gates

- `main == prod` must remain true for backend Edge Function work.
- Any Edge Function adoption of `_shared` remains deploy-coupled.
- `automation-execute` rich renderer architecture is resolved by ADR-0003 Option C and PR #44.
- `automation-execute` adoption is now unlocked, but must be a separate deploy-coupled PR and must stop before deploy for Victor approval.
- `landing-lead` is public and must remain the last adoption target.
- Product Core UI may continue after EPIC 3 completion.

### Next recommended EPIC

EPIC 3 Loop 2 — Queue Area 3: CRM / Contacts / Deals UI Readiness.

Goal: prepare the visible Product Core CRM UI for real-data flows without introducing new backend drift.

Scope:

- Contacts UI readiness.
- Deals UI readiness.
- CRM queue states.
- Pipeline UI consistency.
- Loading, empty and error states.
- Translation/i18n continuity after EPIC 3.
- Module Registry alignment in visible CRM areas.
- No new backend model changes unless separately gated.
- No AI agents in this loop.

Exit criteria:

- CRM, Contacts and Deals screens are consistent, usable and ready to consume real data.
- No mock-only UI behavior is presented as production behavior.
- Module availability, labels, route guards and translations are aligned.
- Antenor publishes a completion report with branch, commit, PR, tested flows and blockers.

## Phase 0 — Current gates and stabilization

Goal: finish the currently open identity, backend resilience and coordination work before moving to new platform layers.

Required outcomes:

- Keep all production deploys, merges and migrations behind gate approval.
- Maintain repo/prod reconciliation for deployed Edge Functions.
- Finish shared-layer adoption only with function-by-function deploy-coupled gates.
- Adopt `automation-execute` only after the PR #44 rich renderer merge, using a separate deploy-coupled PR.
- Keep public endpoints, especially `landing-lead`, as high-risk final targets.

Exit criteria:

- All active PRs are reviewed.
- Identity and backend resilience rules are documented.
- No known drift remains between repository and production for critical functions.

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

Current Phase 3 loop:

- EPIC 3 Loop 2 — Queue Area 3: CRM / Contacts / Deals UI Readiness.

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

Current automation infrastructure state:

- `automation-retry` adopted `_shared/templateEngine.ts` and was deployed as v8.
- `automation-scheduler` adopted `_shared/templateEngine.ts` and was deployed as v24.
- `_shared/templateEngine.ts` now includes `RenderResult` and `renderAutomationTemplate` from ADR-0003 Option C.
- `renderTemplate` remains a compatible string-returning wrapper.
- `automation-execute` adoption is the next backend automation target and must be deploy-coupled.
- `landing-lead` must remain last because it is public.

Next automation step:

- Open a separate PR for `automation-execute` adoption.
- Remove the inline rich renderer only after validating equivalence with `_shared/renderAutomationTemplate`.
- Run `deno check` and `deno test`.
- Validate against deployed version.
- Stop before deploy for explicit Victor approval.

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
