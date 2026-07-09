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
- `automation-execute` shared template adoption, ADR-0003 Option C: PR #45 merged and deployed as v23. **Template-engine shared-layer adoption is now COMPLETE across `automation-retry` (v8), `automation-scheduler` (v24) and `automation-execute` (v23).** `main == prod`, no drift.

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

### Antenor Product Core frontend queue

Master tracker: product issue #46.

Official sequence until Product Core frontend is commercially usable:

1. Issue #47 — EPIC A1: CRM / Contacts / Deals UI Readiness.
2. Issue #48 — EPIC A2: CRM Interaction Polish.
3. Issue #49 — EPIC A3: Finance / Dashboard UI Readiness.
4. Issue #50 — EPIC A4: Calendar / Messages / Tasks Product Flow.
5. Issue #51 — EPIC A5: Settings / Team / Billing Surface Readiness.
6. Issue #52 — EPIC A6: Product Core QA / Commercial Polish.

Rules for Antenor:

- Work one EPIC at a time unless Victor/ChatGPT explicitly splits work.
- Each EPIC must use a dedicated branch and PR.
- Each EPIC must publish a report in `agent-room/reports/`.
- No backend, Edge Function, migration, RLS or production-data change without separate gate.
- No mock should be presented as production data.
- Stop and report if a frontend task depends on missing backend/schema work.

Current Antenor next action:

- Start Issue #47 — EPIC A1: CRM / Contacts / Deals UI Readiness.

### Current gates

- `main == prod` must remain true for backend Edge Function work.
- Any Edge Function adoption of `_shared` remains deploy-coupled.
- `automation-execute` rich renderer architecture is resolved by ADR-0003 Option C and PR #44.
- `automation-execute` adoption is DONE (PR #45, deployed v23). Shared-template adoption complete.
- `landing-lead` is a PUBLIC lead-capture endpoint. It does NOT use the template engine (no message templates), so there is no template adoption for it. Its public/high-risk status is handled by the SECURITY remediation (`sync-whatsapp-history` / `lead-intake` stubs, PR #34), not by template adoption.
- Product Core frontend continues through Antenor issues #46–#52.

### Active EPICs — 2026-07-09 (approved by Victor; ChatGPT role temporarily held by Claude)

Two EPICs run in parallel, one per agent ownership, no cross-boundary work.

#### EPIC A — Antenor (frontend)

EPIC A1 — CRM / Contacts / Deals UI Readiness.

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

#### EPIC B — Claude (backend / platform)

EPIC — Backend Automation Shared-Layer Finalization + Security Remediation Close-out.

Goal: close the open backend security remediation and finish the shared-layer / legacy cleanup, without introducing repo↔prod drift. Security first.

Scope (security-first ordering):

1. Security close-out. Non-gated first: review/classify the remaining public "REVER" Edge Functions (`whatsapp-proxy`, `automation-scheduler/retry/bulk-send/campaign-control`, `setup-org-storage`, `automation-engine`, `lead-intake`) for own-auth; prepare any missing remediation PRs; document residual risk. Gated (stop for Victor): deploy PR #34 (`sync-whatsapp-history` / `lead-intake` 410 stubs), deploy PR #35 (`whatsapp-webhook` shared-secret), rotate `WHATSAPP_GLOBAL_API_KEY`, clean the 2 `"Simulated Insert"` rows in `messages`.
2. Shared-layer cleanup (deploy-coupled): adopt `_shared/whatsappIdentity.ts` in `whatsapp-webhook` and `whatsapp-send` (`normalizeJid` / `getPhone` / `normalizePhone`), same additive→adopt flow as the template engine (additive PR merged first, then function-by-function deploy-coupled adoption).
3. `automation-engine` legacy decision: stub vs keep (major architecture decision → ADR + gate).
4. No new product model changes. No frontend. No AI agents. No Stripe.

Gates (Hard, stop for Victor):

- Any deploy, key rotation, or production-data cleanup.
- `automation-engine` stub/keep is an architecture decision.
- Every shared-layer adoption is deploy-coupled and stops before deploy.

Exit criteria:

- Debug/public-writer incident fully closed (stubs deployed, key rotated, junk cleaned) or explicitly deferred with documented residual risk.
- `whatsappIdentity` duplication removed from active WhatsApp functions.
- `automation-engine` fate decided and recorded (ADR).
- Claude publishes a report per function with repo↔prod verified (`main == prod`).

### EPIC C — WhatsApp Cloud API (Official Meta) Integration — 2026-07-09 (new; Meta app verified)

Design + decisions: **ADR-0005** (keyros-engine). Big architecture shift: from Evolution
(unofficial) to the official Meta Cloud API. Phased, provider-abstracted, no big-bang.
**Decisions D1–D5 (ADR-0005) must be confirmed by Victor before Phase 1.** Meta credentials
inventory needed to start (App ID/Secret, WABA ID, Phone Number ID, System User token,
verify token, Graph API version).

Impact on existing plan: Evolution webhook hardening (#35) and Evolution key rotation drop
to **low priority / frozen** (Evolution becomes legacy). EPIC A (Antenor CRM UI) and EPIC B
(Claude backend cleanup) continue in parallel where they don't touch WhatsApp channels.

Phases (owner · gate):

- **C0 — Decisions & Meta setup** (Victor · decision gate). Confirm D1–D5; provide Meta
  credentials; create/verify the verify-token and app-secret; decide first number(s).
- **C1 — Data model & secrets** (Claude · migration gate). `whatsapp_channels`
  (provider/phone_number_id/waba_id/encrypted token/verify_token/status, org+RLS);
  `whatsapp_templates` registry; `whatsapp_messages.provider` + status mapping; token
  encryption (Vault/pgsodium). Migrations written, stop before apply.
- **C2 — Provider abstraction** (Claude · non-gated → deploy-coupled). `_shared/whatsappProvider.ts`
  interface + `evolutionProvider.ts` (wrap existing) + `cloudApiProvider.ts`. Route by channel.
- **C3 — Inbound Cloud webhook** (Claude · deploy gate). New `whatsapp-cloud-webhook`:
  GET verification (`hub.verify_token`/`hub.challenge`) + POST with mandatory
  `X-Hub-Signature-256` (HMAC-SHA256, app secret) validation; normalize Meta payload →
  existing contacts/chats/messages/automations pipeline; delivery statuses.
- **C4 — Outbound send + 24h window** (Claude · deploy gate). `cloudApiProvider.sendText`/
  `sendTemplate` via `graph.facebook.com/{phone_number_id}/messages`; enforce the 24-hour
  customer-service window (free-form inside, template outside); wire `whatsapp-send`/
  `automation-execute`/`automation-scheduler` through the provider.
- **C5 — Templates & automations** (Claude · gate). Template sync from Meta + registry;
  automation actions select a template + map variables to components; scheduler/execute
  respect the window.
- **C6 — Onboarding & UI** (Antenor · frontend). Connect-channel flow (manual first →
  Embedded Signup later), channel status, template management UI, per-org provider display.
- **C7 — Migration & coexistence** (Claude+Antenor · gates). Per-org provider switch; data
  continuity; keep Evolution as fallback, then deprecate/remove Evolution functions.

Exit criteria: a studio can connect its own Meta WhatsApp number, receive and send
(respecting the 24h/template rules) through the CRM, with delivery statuses and tenant
isolation; Evolution is legacy/removable; tokens encrypted; webhook signature-verified.

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

Current Phase 3 frontend sequence:

- A1: CRM / Contacts / Deals UI Readiness — issue #47.
- A2: CRM Interaction Polish — issue #48.
- A3: Finance / Dashboard UI Readiness — issue #49.
- A4: Calendar / Messages / Tasks Product Flow — issue #50.
- A5: Settings / Team / Billing Surface Readiness — issue #51.
- A6: Product Core QA / Commercial Polish — issue #52.

Exit criteria:

- Dashboard uses real data or explicitly labeled placeholders.
- Deals can drive financial understanding.
- Core CRM flows can be used by a real business without mock-only behavior.
- Product Core frontend can be used as a commercial minimum product.

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
- `automation-execute` adopted `_shared/templateEngine.ts` (rich `renderAutomationTemplate`) and was deployed as v23 (PR #45).
- `landing-lead` does not use the template engine (no message templates); it is a public lead-capture endpoint handled by the security remediation, not by template adoption.

Automation shared-layer status: COMPLETE.

- `automation-retry` (v8), `automation-scheduler` (v24) and `automation-execute` (v23) all import `_shared/templateEngine.ts`. Template-engine duplication is eliminated; `main == prod` for all three.
- Remaining backend cleanup is tracked in **EPIC B** (whatsappIdentity adoption; `automation-engine` legacy decision) and the security close-out.

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
