# STATE

phase: intake | building | integrating | complete
updated: <YYYY-MM-DD>

> Source of truth for routing. `MASTER_PROMPT.md` reads this first, every session.
> Every phase updates it before finishing.

## Sub-modules

Listed in build order.

| # | id | status | files | notes |
|---|---|---|---|---|
| 1 | `<id>` | pending | | |
| 2 | `<id>` | pending | | |

**Statuses:** `pending` → `in-progress` → `built` → `verified`, plus `stale`.

- `built` — implemented and tests pass, but conformance or self-review left something
  outstanding. The note says what.
- `verified` — contract conformance, tests, and invariants self-review all passed.
- `stale` — a contract it depends on changed after it was built. Re-run Phase 3.

## Integration

| Scope | Status | Notes |
|---|---|---|
| `<module>` | pending | |
| application | pending | |

## Open blockers

- `<id>` — stopped on `Q-<MODULE>-<n>` in `_incomplete.md`
- `<id>` — stopped on `D-<nnn>` in `_drift.md`

Empty means nothing is waiting on the user.

## Log

- `<YYYY-MM-DD>` — <phase> — <what happened>
