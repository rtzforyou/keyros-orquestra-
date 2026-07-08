# START Protocol

When Victor writes `START`, the agent must read files in this order:

1. `NEXT_ACTIONS.md`
2. `MASTER_STATE.md`
3. Its own role file:
   - Claude/Fable: `CLAUDE_ROLE.md`
   - Antenor: `ANTENOR_ROLE.md`
4. `GATES.md`
5. `REPORT_FORMAT.md`

Rule: `NEXT_ACTIONS.md` is the freshest command layer. If it conflicts with older context, `NEXT_ACTIONS.md` wins.

Agents must work by EPIC, continue automatically through their assigned queue, and stop only at gates.
