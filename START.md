# START Protocol

When Victor writes `START`, the agent must operate in roadmap-driven autonomous mode.

## Read order

Agents must read files in this exact order:

1. `ROADMAP.md`
2. `MASTER_STATE.md`
3. Its own role file:
   - Claude/Fable: `CLAUDE_ROLE.md`
   - Antenor: `ANTENOR_ROLE.md`
4. `GATES.md`
5. `REPORT_FORMAT.md`
6. `NEXT_ACTIONS.md` only if it exists and contains a fresher explicit override from Victor/ChatGPT
7. Relevant `docs/knowledge/` files for the selected EPIC/module

## Source of direction

- `ROADMAP.md` defines where Keyros goes.
- `MASTER_STATE.md` defines where Keyros is now.
- `GATES.md` defines where the agent must stop.
- `REPORT_FORMAT.md` defines how the agent reports.
- `NEXT_ACTIONS.md` is optional and temporary. It must not be required for normal execution if the roadmap and master state are clear.

## Autonomous work-queue model

Agents must not treat a finished sprint, opened draft PR or report as a final stop.

The operating model is a continuous work queue:

```text
select eligible work
  ↓
create/update branch
  ↓
implement a coherent safe batch
  ↓
small commits
  ↓
open/update draft PR
  ↓
write/update agent-room report
  ↓
check Hard Gates
  ↓
if no Hard Gate: continue to next eligible work
```

A draft PR is a checkpoint, not a stop condition.

A report is a checkpoint, not a stop condition.

A completed sprint is a checkpoint, not a stop condition.

## Autonomous EPIC selection

After reading the required files, each agent must:

1. Identify the next eligible EPIC or work item from `ROADMAP.md`, `MASTER_STATE.md`, `NEXT_ACTIONS.md` and recent reports.
2. Compare it with current repository state.
3. Work only inside its ownership boundary.
4. Continue automatically through safe tasks until it reaches a Hard Gate or the continuous work limit.
5. Never skip roadmap phases unless Victor/ChatGPT explicitly approved the change.
6. Never start a blocked EPIC.

## Checkpoint rule

Agents must create checkpoints frequently to avoid hidden drift.

A checkpoint means:

1. Commit small changes.
2. Open or update a draft PR.
3. Write or update an `agent-room/reports/` report.
4. Continue automatically if no Hard Gate is crossed.

Recommended checkpoint triggers:

- one coherent feature/refactor batch completed
- more than 10 files changed
- more than 5 commits accumulated
- one module completed
- one i18n/UX/backend area completed
- before moving to a different domain or module

Checkpoint does not require Victor/ChatGPT approval unless a Hard Gate is reached.

## PR safety rule

Agents must never work directly on `main`.

Required execution flow:

```text
ROADMAP / MASTER_STATE / NEXT_ACTIONS
  ↓
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
  ↓
merge only after approval
```

If the work becomes wrong, too large, or unsafe, the branch/PR can be closed without damaging `main`.

## Stop rule

Agents must stop only when one of these happens:

- a Hard Gate in `GATES.md` is reached
- the continuous work limit in `GATES.md` is reached
- the next action would cross ownership boundaries
- the next action is blocked by another agent or PR conflict
- the agent is uncertain whether the next step is safe
- there is no remaining eligible work inside its ownership boundary

If in doubt, stop and report the blocker instead of guessing.