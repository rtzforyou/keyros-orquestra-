# GATES

Keyros Orquestra uses two kinds of gates:

1. **Hard Gates** — agents must stop and wait for Victor/ChatGPT.
2. **Soft Continuation** — agents must report, open/update draft PRs, then continue automatically to the next eligible EPIC if no Hard Gate is crossed.

## Hard Gates

Agents must stop and ask Victor or ChatGPT before:

- merge to main
- production deploy
- production data change
- database migration apply
- auth or access boundary change
- payment provider implementation
- destructive operation
- cross-agent conflict
- major architecture decision
- changing roadmap order
- starting a blocked EPIC
- crossing ownership boundaries

## Soft Continuation

Agents must **not** stop only because:

- an EPIC loop finished
- a report was created
- a draft PR was opened
- the next roadmap EPIC is available
- the next work is inside the same agent ownership boundary
- the next work does not require merge, deploy, migration, production mutation, auth/RLS/billing boundary change, destructive operation or major architecture decision

In those cases, the agent must:

```text
finish loop
  ↓
commit small changes
  ↓
open or update draft PR
  ↓
write agent-room report
  ↓
select next eligible EPIC from ROADMAP + MASTER_STATE
  ↓
continue automatically
```

## Continuous Work Limit

To avoid unbounded autonomous drift, each agent may continue automatically until the first of these happens:

- a Hard Gate is reached
- 3 consecutive EPICs/major loops are completed without human review
- 24 hours of continuous work has passed
- the agent is uncertain whether the next step is safe

After that, the agent must stop and report.

## Permanent safety rules

Agents must never:

- work directly on `main`
- push directly to `main`
- merge their own PR
- deploy production without approval
- apply migrations without approval
- mutate production data without approval
- implement payment provider logic without approval
- change auth, RLS, permissions or tenant isolation boundaries without approval
- ignore a known blocker from another agent
- continue when the next action would cross ownership boundaries

## Required delivery shape

All non-trivial work must be isolated as:

```text
new branch
  ↓
small commits
  ↓
draft PR
  ↓
agent-room report
  ↓
Hard Gate review only when required
```

If a branch becomes unsafe or wrong, close the PR and discard the branch. `main` must remain recoverable and clean.

If in doubt, stop and report the blocker.