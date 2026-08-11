# PHASE 2 — PLAN (and AMEND)

**Goal:** convert answered questions into frozen invariants, a final module map, and a complete
contract for every sub-module — *before any code exists*.

This phase is why the system works. A build session never invents a contract; it implements one.

**You write no application code in this phase. None.**

---

## Mode select

- `_drift.md` has an `APPROVED` entry → **AMEND mode**. Jump to "Amend mode" at the bottom.
- Otherwise (`phase: intake`, questions answered) → **PLAN mode**. Run all steps.

---

# PLAN mode

## Step 0 — Gate

Read `_planning/_incomplete.md`. Every `❓` must have a non-empty `**Answer:**`.

If any are unanswered: list them by id, stop, ask the user. Do not partially plan.
Do not proceed on "I'll come back to that one."

If an answer is present but ambiguous, or the answer raises a new decision: append the new
`❓` to the correct section, stop, and ask. Appending is normal and expected; guessing is not.

## Step 1 — `00_invariants.md`

From `system/templates/00_invariants.md`. This file is loaded by **every** build session, so
every line costs tokens forever. Budget: 250 lines.

It contains only what a build session cannot do its job without:
- stack, versions, package manager, runtime target
- directory layout and file placement rules
- naming conventions: files, functions, types, DB objects, routes, env vars
- the error model — how errors are represented, raised, propagated, surfaced
- logging rules
- configuration and secret handling
- security rules that apply everywhere: input validation, authz checks, output encoding,
  what must never be logged
- data layer conventions: migrations, transactions, query patterns
- test framework, test file location, what a test must cover
- dependency policy — adding a dependency not listed here requires a `❓`
- lint/format/test/build commands, exactly as typed
- hard prohibitions

Every entry must trace to an answered question or an explicit statement in the project
description. If you are about to write a convention nobody specified — stop, that is a `❓`.

It is not a design document, an architecture essay, or a rationale log. Rules only.

## Step 2 — Finalize `01_module_map.md`

Revise the draft with the answers now in hand. For each sub-module:

- `id`, purpose (one line)
- `depends_on` — sub-modules whose contracts it calls
- `consumed_by` — derived, must be the exact inverse of all `depends_on` (verify this)
- `contract` — path to its contract file
- `owns` — the source paths this sub-module and only this sub-module may write

Then:
- Verify the dependency graph is **acyclic**. A cycle means the boundary is wrong — re-cut it,
  or extract the shared surface into its own sub-module.
- Compute **build order** by topological sort, leaves first. Ties broken by fewest dependents.
- Verify no sub-module exceeds ~5 neighbors (`depends_on` + `consumed_by`).

Set `status: frozen`.

## Step 3 — Write every contract

One file per sub-module at `_planning/02_contracts/<id>.md`, from
`system/templates/contract.md`. Budget: 150 lines.

A contract declares **surface only**:

- exported functions/classes/types with exact signatures and types
- HTTP routes: method, path, request schema, response schema, status codes
- events emitted and consumed, with payload shapes
- persistent state touched: tables/collections/keys, read vs write
- errors raised: exact names/codes, and the condition for each
- required env vars
- side effects: external calls, filesystem, queues, timers
- guarantees a consumer may rely on (ordering, idempotency, atomicity, nullability)
- **non-goals** — what this sub-module explicitly does not do

A contract never contains: implementation steps, algorithm choices, internal helper functions,
example bodies, or prose explaining how to build it. If a consumer cannot observe it, it does
not belong in the contract.

**Consistency pass — this is where integration bugs are prevented.** For every `depends_on`
edge, read both contracts side by side and confirm: the exported signature the consumer calls
exists with exactly that name and shape; the types match on both sides; every error the
provider raises is one the consumer's declared behavior accounts for; shared data shapes are
identical field for field, not merely similar. Fix mismatches now. This pass is cheap here and
expensive in Phase 4.

Over budget → the sub-module is too big. Split it, update the map, re-derive build order.

## Step 4 — Initialize `_state.md`

From the template. `phase: building`. Every sub-module `pending`, in build order.

## Step 5 — Report and stop

Tell the user: sub-module count, build order, any sub-module you split and why, and the first
sub-module to build. Give them the exact invocation:

```
Follow _planning/MASTER_PROMPT.md — build <first-id>
```

Contracts are now frozen. From here they change only through `_drift.md`.

---

## PLAN exit checklist

- [ ] Every `❓` answered; no new unanswered ones remain
- [ ] `00_invariants.md` ≤ 250 lines, every rule traceable to an answer
- [ ] Dependency graph acyclic; `consumed_by` is the exact inverse of `depends_on`
- [ ] Every sub-module has a contract ≤ 150 lines
- [ ] Consistency pass done on every dependency edge
- [ ] No contract contains implementation guidance
- [ ] `owns` paths do not overlap between sub-modules
- [ ] Build order is a valid topological sort
- [ ] Zero lines of application code written

---

# AMEND mode

Triggered by an `APPROVED` entry in `_drift.md`. You are the only authority allowed to change
a frozen contract.

1. Read the approved entry: which contract, declared vs. required, the user's ruling.
2. Apply the change to that contract file. Minimum edit — change what the ruling requires and
   nothing more.
3. Re-run the Step 3 consistency pass on **every edge touching that contract**.
4. For each affected consumer or provider whose status is `built` or `verified`: set it to
   `stale` in `_state.md` and note which contract moved.
5. If the ruling changes a global convention, update `00_invariants.md`. If it changes the
   decomposition, update `01_module_map.md` and re-derive build order.
6. If the ruling raises a new open decision, append a `❓` to `_incomplete.md`, stop, and ask.
7. Mark the drift entry `RESOLVED` with the date and what changed.
8. Report: what changed, and the full list of now-`stale` sub-modules to rebuild.

Never resolve a drift entry that is still `OPEN`. The user rules on drift, not you.
