# NEXT ACTIONS

Date: 2026-07-09
Freshness rule: this file wins over older chat context when there is conflict.

Victor is offline / away. Agents must use autonomous work-queue mode and checkpoint frequently.

A finished sprint, opened draft PR or report is not a stop condition. It is a checkpoint.

---

# Global Autonomous Rule

Agents must run in continuous work-queue mode:

```text
eligible work
  ↓
implementation batch
  ↓
small commits
  ↓
draft PR checkpoint
  ↓
report checkpoint
  ↓
continue automatically when safe
```

Stop only for a Hard Gate, ownership conflict, blocked work, uncertainty, no remaining eligible work, or the continuous work limit in `GATES.md`.

Draft PRs and reports must be created early to prevent large hidden branches, but they do not require the agent to stop.

---

# CURRENT STATE SNAPSHOT

## Completed / merged

- PR #29 has been merged. Backend resilience repo↔production reconciliation is complete for webhook/send/resilience path.
- `main` was reported healthy after #29: `npx tsc --noEmit` clean and production build successful.
- Critical debug endpoints `debug-whatsapp`, `debug-api`, and `diagnostic` were neutralized in production with 410 Gone kill-stubs.

## Active / open

- PR #31: Antenor clean frontend i18n consolidation. Draft, frontend-only expected, needs review/continuation.
- PR #32: Claude docs/platform inventory. Draft/open docs PR.
- PR #33: security remediation kill-stubs. Draft/open code source for already deployed stubs; must be reviewed/merged/reconciled through normal gate.
- Engine reports/PRs exist for security and platform docs.

## Blocked / not approved

- PR #30 is not approved. It is stale/dirty and must not be merged. Recover useful frontend work only through clean PRs.

---

# Claude Native Command — Security and Platform Sequence

## START command

```text
START

Run in continuous autonomous work-queue mode.

Read in order:

1. ROADMAP.md
2. MASTER_STATE.md
3. CLAUDE_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. Relevant docs/knowledge/

Draft PRs and reports are checkpoints, not stopping conditions.

Continue automatically through eligible backend/platform/security work inside Claude ownership.

Stop only for Hard Gates, ownership conflict, blocked work, uncertainty, no remaining eligible backend work, or continuous work limit.
```

## Claude sequence

### 1. Security remediation source reconciliation

1. Verify PR #33 contains only the 410 Gone kill-stubs for:
   - `debug-whatsapp`
   - `debug-api`
   - `diagnostic`
2. Verify those production functions are still neutralized.
3. Ensure PR #33 contains no unrelated changes.
4. Update the security report if needed.
5. Do not deploy again unless a security gate explicitly requires it.
6. Do not delete the function slugs yet.
7. Do not rotate keys yet.
8. Do not clean production data yet.

Checkpoint with draft PR/report, then continue.

### 2. sync-whatsapp-history remediation preparation

Prepare the smallest safe remediation for `sync-whatsapp-history` because it is public and can create production deals/contacts.

Allowed:

- create a 410 Gone kill-stub PR for `sync-whatsapp-history`
- document why it is legacy/high-risk
- prepare verification steps
- prepare rollback plan

Not allowed without explicit gate:

- deploy
- delete function
- mutate production
- clean data
- rotate keys

Checkpoint with draft PR/report, then stop if deploy is required.

### 3. Public Edge Function auth inventory

Audit every deployed Edge Function with `verify_jwt=false` and classify it:

- required with valid custom auth
- webhook requiring external signature/secret
- legacy but harmless
- legacy risky
- debug/diagnostic
- unknown

For each function, report:

- whether it uses `service_role`
- whether it reads tenant data
- whether it writes production data
- whether it has custom auth
- recommended action

Do not call dangerous endpoints. Prefer source review and metadata inspection.

Checkpoint with draft PR/report, then continue.

### 4. Webhook spoofing review

Review `whatsapp-webhook` security posture.

Goal:

- confirm whether Evolution API requests are authenticated with a shared secret or equivalent
- if missing, prepare a minimal design/PR for webhook signature or token validation

Hard stop before:

- auth/access boundary change
- deploy
- secret rotation
- production mutation

Checkpoint with draft PR/report, then continue if still safe.

### 5. Legacy Edge / Migration Cleanup proposal

Continue documenting the legacy drift discovered:

- deployed-only functions
- repo-only functions
- migration ledger drift

Do not delete or rewrite history automatically.

Prepare a decision document with options:

- keep as legacy with stubs
- delete slugs
- reconcile source files
- rename/deprecate
- ignore if harmless

Checkpoint with draft PR/report, then continue.

## Claude hard boundaries

Claude must stop before:

- merge to main
- production deploy
- database migration apply
- production data mutation or cleanup
- key/secret rotation
- auth/RLS/access boundary change
- Billing Engine / Stripe work
- destructive operation
- crossing Antenor ownership

---

# Antenor Native Command — Frontend Sequence

## START command

```text
START

Run in continuous autonomous work-queue mode.

Read in order:

1. ROADMAP.md
2. MASTER_STATE.md
3. ANTENOR_ROLE.md
4. GATES.md
5. REPORT_FORMAT.md
6. NEXT_ACTIONS.md
7. docs/knowledge/29-translation-architecture.md
8. Relevant frontend/module docs/knowledge/

Synchronize with latest main before continuing.

Draft PRs and reports are checkpoints, not stopping conditions.

Continue automatically through eligible frontend/i18n/UX work inside Antenor ownership.

Stop only for Hard Gates, ownership conflict, blocked work, uncertainty, no remaining eligible frontend work, or continuous work limit.
```

## Antenor sequence

### 1. PR #31 continuation and verification

1. Sync PR #31 with latest `main`.
2. Verify PR #31 is frontend-only.
3. Ensure PR #31 does not include:
   - Supabase migrations
   - Edge Functions
   - backend logic
   - RLS/auth changes
   - Billing Engine / Stripe logic
   - production data changes
   - `.claude` files
4. Run typecheck/build.
5. Update the frontend report.
6. Continue automatically if no Hard Gate is reached.

### 2. Continue remaining frontend i18n debt

Continue replacing visible hardcoded strings with translation IDs in:

- Messages
- CRM
- Contacts
- Deals/Pipeline
- Calendar
- Team
- Settings
- Billing UI
- Support dialog
- Integrations panels
- Dashboard subcomponents

Rules:

- visible UI text must use translation IDs
- business logic must use stable IDs/codes
- do not translate enums, stage IDs, module keys, route keys or database values
- add keys for all supported locales already present in repo

Checkpoint with draft PR/report, then continue.

### 3. UI status feedback cleanup

Replace remaining browser-native UI patterns with inline Keyros UI:

- `alert()`
- `confirm()`
- raw browser prompts
- unstyled success/failure messages

Use:

- inline status banners
- modal success states
- toast/status components if already available
- accessible error states

Do not introduce broad design rewrites.

Checkpoint with draft PR/report, then continue.

### 4. CRM / Contacts / Deals UI readiness

Audit and improve frontend-only states:

- loading
- empty
- error
- permission denied
- plan locked
- read-only
- mobile layout
- drawer/modal labels
- action buttons

Do not implement new backend data sync or Product Core behavior yet unless explicitly unlocked.

Checkpoint with draft PR/report, then continue.

### 5. Dashboard polish and consistency

Audit and improve frontend-only dashboard UI:

- chart labels
- tooltips
- empty states
- loading states
- error states
- responsive layout
- card spacing
- metric naming consistency

Do not change calculations or backend queries.

Checkpoint with draft PR/report, then continue.

### 6. Accessibility and responsive pass

Audit and fix safe issues:

- aria-labels
- keyboard focus
- contrast
- mobile overflow
- tablet layout
- desktop consistency
- drawer/modal navigation

Checkpoint with draft PR/report, then continue.

### 7. Frontend documentation

Update docs only where behavior/rules changed:

- `docs/knowledge/03-design-principles.md`
- `docs/knowledge/05-dashboard.md`
- `docs/knowledge/24-ux-rules.md`
- `docs/knowledge/29-translation-architecture.md`

Checkpoint with draft PR/report, then continue.

## Antenor hard boundaries

Antenor must stop before:

- backend work
- Supabase changes
- Edge Functions
- migrations
- production deploy
- production data mutation
- auth/RLS/permission boundary change
- Billing Engine / Stripe logic
- destructive operation
- crossing Claude ownership

---

# Review rule for Victor/ChatGPT when back online

Review priority:

1. Security PRs first: PR #33 and any `sync-whatsapp-history` remediation PR.
2. Then PR #31 frontend i18n/UX.
3. Then PR #32 docs/platform inventory.
4. Then decide Legacy Edge/Migration Cleanup.
5. Only after security + frontend debt are under control, unlock Phase 3 Product Core.
