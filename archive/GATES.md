# GATES

Keyros Orquestra uses two execution controls:

1. **Hard Gates** — agents must stop and wait for Victor/ChatGPT.
2. **Autonomous Checkpoints** — agents must commit, open/update draft PRs, report, then continue automatically if no Hard Gate is crossed.

## Hard Gates

Agents must stop and ask Victor or ChatGPT before:

- merge to `main`
- production deploy
- production data change
- database migration apply
- auth, RLS, permissions, tenant isolation or access boundary change
- payment provider implementation
- Billing Engine provider/payment logic
- destructive operation
- cross-agent conflict
- major architecture decision
- changing roadmap order
- starting a blocked EPIC
- crossing ownership boundaries

These gates are safety gates. They must not be removed for speed.

## Not a stopping condition

Agents must **not** stop only because:

- a sprint finished
- an EPIC loop finished
- a report was created
- a draft PR was opened
- a draft PR was updated
- a coherent batch was committed
- the next roadmap EPIC is available
- the next work is inside the same agent ownership boundary
- the next work does not require merge, deploy, migration, production mutation, auth/RLS/billing boundary change, destructive operation or major architecture decision

In those cases, the agent must continue automatically.

## Autonomous checkpoint loop

Agents must use this loop:

```text
select eligible work
  ↓
implement safe coherent batch
  ↓
commit small changes
  ↓
open/update draft PR
  ↓
write/update agent-room report
  ↓
check Hard Gates
  ↓
continue automatically if no Hard Gate exists
```

A draft PR is a checkpoint, not a stop.

A report is a checkpoint, not a stop.

A completed sprint is a checkpoint, not a stop.

## Checkpoint frequency

To avoid the confusion created by very large branches, agents must checkpoint early.

Recommended checkpoint triggers:

- one coherent batch completed
- more than 10 files changed
- more than 5 commits accumulated
- one module completed
- before switching domain/module
- before doing work that might become harder to review later

Checkpointing does not require waiting for Victor/ChatGPT unless a Hard Gate is reached.

## Continuous Work Limit

To avoid unbounded autonomous drift, each agent may continue automatically until the first of these happens:

- a Hard Gate is reached
- 3 consecutive EPICs/major loops are completed without human review
- 24 hours of continuous work has passed
- the agent is uncertain whether the next step is safe
- there is no remaining eligible work inside the agent ownership boundary

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
draft PR checkpoint
  ↓
agent-room report checkpoint
  ↓
continue automatically when safe
  ↓
Hard Gate review only when required
```

If a branch becomes unsafe or wrong, close the PR and discard the branch. `main` must remain recoverable and clean.

If in doubt, stop and report the blocker.
