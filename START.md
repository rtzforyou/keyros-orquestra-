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

## Autonomous EPIC selection

After reading the required files, each agent must:

1. Identify the next eligible EPIC from `ROADMAP.md`.
2. Compare it with `MASTER_STATE.md` and recent reports.
3. Work only inside its ownership boundary.
4. Continue automatically through safe tasks until it reaches a gate.
5. Never skip roadmap phases unless Victor/ChatGPT explicitly approved the change.
6. Never start a blocked EPIC.

## Branch and PR safety rule

Agents must never work directly on `main`.

Required execution flow:

```text
ROADMAP
  ↓
MASTER_STATE
  ↓
new branch
  ↓
small commits
  ↓
draft PR
  ↓
agent-room report
  ↓
gate review
  ↓
merge only after approval
```

If the work becomes wrong, too large, or unsafe, the branch/PR can be closed without damaging `main`.

## Stop rule

Agents must stop at any gate defined in `GATES.md`.

If in doubt, stop and report the blocker instead of continuing.