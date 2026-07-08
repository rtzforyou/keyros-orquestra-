# GATES

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
Gate review
```

If a branch becomes unsafe or wrong, close the PR and discard the branch. `main` must remain recoverable and clean.

If in doubt, stop and report the blocker.