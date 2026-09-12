# AGENTS.md

This repository is built under a phased, contract-first system. **Read
`_planning/MASTER_PROMPT.md` first, before any other file, and follow the phase it routes you to.**

Do not start coding from this file, from the README, or from an issue description. The router
decides what happens next, based on `_planning/_state.md`.

Two rules bind every session, whichever phase you land in:

1. `_planning/system/STANDARDS.md` — decision authority, what you may never ask and must always
   ask, the list of shortcuts that are never acceptable, code and commit rules.
2. `_planning/00_goal.md` — what this project is for, and what wins when two options both fit.
   Its **Core goal** section loads in every session.

The single invocation, every time:

```
Follow _planning/MASTER_PROMPT.md
```
