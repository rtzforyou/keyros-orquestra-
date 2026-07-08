# NEXT ACTIONS

Date: 2026-07-08
Freshness rule: this file wins over older chat context when there is conflict.

## Global direction

Current priority: finish identity stabilization, then build the Keyros Knowledge Base before expanding product modules.

Do not start unrelated modules until the Knowledge Base EPIC is completed or explicitly approved by Victor/ChatGPT.

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

## Active EPIC 0 — WhatsApp Identity Finalization

### Claude tasks

1. Review PR #17 backend changes.
2. Confirm group identity uses `group.subject` only.
3. Confirm group identity never depends on `sender_name`, `pushName`, participant `display_name` or the latest message participant.
4. Confirm direct conversation identity still uses the corrected interlocutor logic from PR #16.
5. Add or verify tests/fixtures for:
   - direct incoming message
   - direct `fromMe` message
   - group message
   - group `fromMe` message
   - channel or unknown fallback
6. Report:
   - branch
   - commit SHA
   - PR
   - tests run
   - remaining blockers

Claude must stop before merge or production deploy.

### Antenor tasks

1. Verify Messages UI after PR #17 from the frontend side.
2. Confirm group names render from canonical conversation identity.
3. Confirm participant names may appear inside messages but never replace the conversation title.
4. Validate safe states for messages:
   - loading
   - empty
   - error
   - direct conversation
   - group conversation
5. Check mobile layout for conversation list and chat area.
6. Fix safe frontend-only issues if found.
7. Report:
   - current loop area
   - branch
   - commit SHA
   - PR if code changed
   - report path
   - blockers
   - next loop area

Antenor must not change backend identity logic.

## Active EPIC 1 — Keyros Knowledge Base

### Objective

Create the official documentation brain inside the product repository first. Do not create a standalone repository yet.

Target path in product repo:

```text
docs/knowledge/
```

### Claude tasks — backend/architecture documentation

Maximum 15–20 tasks per loop.

1. Create or update `docs/knowledge/00-introduction.md`.
2. Create or update `docs/knowledge/02-system-architecture.md`.
3. Create or update `docs/knowledge/09-whatsapp.md`.
4. Create or update `docs/knowledge/12-billing.md`.
5. Create or update `docs/knowledge/13-module-registry.md`.
6. Create or update `docs/knowledge/16-security.md`.
7. Create or update `docs/knowledge/17-database.md`.
8. Create or update `docs/knowledge/19-edge-functions.md`.
9. Create or update `docs/knowledge/20-testing.md`.
10. Create or update `docs/knowledge/21-runbooks.md`.
11. Document current confirmed behavior only.
12. Mark planned behavior as `Planned`, not as implemented.
13. Include AI interpretation rules in each backend-owned document.
14. Add references to RLS, audit, rate limiter, module registry and identity protection where relevant.
15. Report branch, commit SHA, PR and blockers.

Claude must stop before migrations, deploys, auth boundary changes or billing provider work.

### Antenor tasks — frontend/product documentation

Maximum 10 tasks per cycle. IMPLEMENTATION FIRST when safe frontend fixes are found.

1. Create or update `docs/knowledge/03-design-principles.md`.
2. Create or update `docs/knowledge/04-crm-overview.md`.
3. Create or update `docs/knowledge/05-dashboard.md`.
4. Create or update `docs/knowledge/06-contacts.md`.
5. Create or update `docs/knowledge/07-deals.md`.
6. Create or update `docs/knowledge/08-messages.md`.
7. Create or update `docs/knowledge/10-calendar.md`.
8. Create or update `docs/knowledge/11-team.md`.
9. Create or update `docs/knowledge/24-ux-rules.md`.
10. Create or update `docs/knowledge/25-troubleshooting.md`.

Rules for Antenor:

- Document what the UI currently does.
- Identify mock/fallback data honestly.
- Fix safe frontend-only issues discovered during documentation.
- Do not change backend rules.
- Do not invent business rules.
- Stop at gates.

## ChatGPT responsibilities

1. Review roadmap alignment.
2. Review PR #17 before merge.
3. Review Knowledge Base structure before merge.
4. Approve or reject gates.
5. Keep architecture consistent with `ROADMAP.md`, `MASTER_STATE.md`, `GATES.md`, and role files.

## Success condition for this cycle

This cycle is successful when:

- PR #17 is reviewed and either approved or returned with changes.
- `docs/knowledge/` exists in the product repo.
- Claude and Antenor each have a scoped queue.
- The system has a written direction after Knowledge Base completion.
