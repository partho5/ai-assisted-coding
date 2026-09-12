# STATE

phase: understood | modules | building | integrating | complete
updated: <YYYY-MM-DD>

> Source of truth for routing. `MASTER_PROMPT.md` reads this first, every session.
> Every phase updates it before finishing.

## Sub-modules

Listed in build order.

| # | id | status | attempts | verdict | files | notes |
|---|---|---|---|---|---|---|
| 1 | `<id>` | pending | 0 | | | |
| 2 | `<id>` | pending | 0 | | | |

**Statuses:** `pending` → `in-progress` → `built` → `verified`, plus `stale`.

- `built` — implemented, tests pass, the build session's own pre-flight passed.
  **Not confirmed by anyone else.** A build session can set this and nothing higher.
- `verified` — a Phase 3.5 session that never saw the building passed it.
  **Only Phase 3.5 may set this.** The `verdict` column names the file that decided it.
- `stale` — a contract it depends on changed after it was built. Re-run Phase 3.

**attempts** — incremented on every verify FAIL. At **3**, the whole project stops (router row 3).
Three failed builds of one sub-module is a contract or decomposition problem, not a coding
problem, and re-attempting it a fourth time degrades the code further.

## Integration

| Scope | Status | Notes |
|---|---|---|
| `<module>` | pending | |
| application | pending | |

## Open blockers

- `<id>` — `Q-<MODULE>-<n>` in `_incomplete.md`, escalated by Phase 2.5 — needs the user
- `<id>` — `D-<nnn>` in `_drift.md` — needs the user to rule

Empty means nothing is waiting on the user.

An open `❓` is not automatically a blocker in v2 — it routes to Phase 2.5 first. List it here
only once Phase 2.5 has escalated it.

## Log

- `<YYYY-MM-DD>` — <phase> — <what happened>
