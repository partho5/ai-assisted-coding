# PHASE 2 — PLAN (and AMEND)

**Goal:** convert answered questions into frozen invariants, a final module map, and a complete
contract for every sub-module — *before any code exists*.

This phase is why the system works. A build session never invents a contract; it implements one.

**You write no application code in this phase. None.**

---

## Mode select

- `_drift.md` has an `APPROVED` entry → **AMEND mode**. Jump to "Amend mode" at the bottom.
- Otherwise (`phase: understood` or `phase: modules`) → **PLAN mode**. Run all steps.

**Asking posture: collect, do not stop.** This is the phase that raises the technical questions —
stack, storage, error model, testing. `STANDARDS.md` §1 binds: decide the never-ask items and log
them, derive what the goal settles, and write a `❓` only for the always-ask list or something
expensive to reverse. Questions you do raise are **appended and left open**. Phase 2.5 answers what
it can and batches the rest into one interruption. Do not stop the project on them.

---

# PLAN mode

## Step 0 — Two gates

**Gate 1 — answered questions.** Read `_planning/_incomplete.md`. Every `❓` must have a non-empty
`**Answer:**`. If any are unanswered, the router should have sent you to Phase 2.5 — stop and say
so rather than planning around a hole.

**Gate 2 — goal coverage.** Read the coverage table in `_planning/01_module_map.md` against the
criteria in `_planning/00_goal.md`.

**You may not freeze contracts while either of these is true:**

- a `G-` criterion no sub-module `satisfies` — the plan cannot reach the goal
- a sub-module that satisfies no criterion — it serves something the goal does not state

Neither is a formatting problem. An orphan criterion means you are about to freeze a plan that
provably cannot deliver what the project is for, and no later phase can detect it. Fix the map, or
raise a `❓` if the fix needs a decision, and stop.

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
- **production parity** — how dev config reaches prod, and the exact list of values that may
  differ at deploy. That list is credentials and environment-specific values only. If anything
  else would have to change before deploying, the configuration is wrong and this is where it
  gets fixed.
- dependency policy — adding a dependency not listed here requires a `❓`
- lint/format/test/build commands, exactly as typed
- hard prohibitions

Every entry must trace to an answered question, a line in `00_goal.md`, or an explicit statement
in the brief. If you are about to write a convention nobody specified, apply `STANDARDS.md` §1:
decide it if it is on the never-ask list and log it, otherwise raise a `❓`.

**Do not restate anything from `system/STANDARDS.md`.** It binds on top of this file and is loaded
in the same sessions. Repeating it wastes the budget that every build session pays forever.

It is not a design document, an architecture essay, or a rationale log. Rules only.

## Step 2 — Finalize `01_module_map.md`

Revise the draft with the answers now in hand. For each sub-module:

- `id`, purpose (one line)
- `satisfies` — the `G-` criteria it helps make true, carried forward from Phase 1
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
- **`satisfies`** — its `G-` criteria, each with the `constrained by:` line **copied verbatim**
  from `00_goal.md`

Copying the constraint lines is what lets a build session know the *specific way* without asking.
Copy them as they are — do not expand one into guidance. One line of constraint is not a recipe,
and turning it into one violates Law 10.

A contract never contains: implementation steps, algorithm choices, internal helper functions,
example bodies, or prose explaining how to build it. If a consumer cannot observe it, it does
not belong in the contract.

**Every contract declares `kind:`.** `declared` is a promise written before the code — everything
above applies. `adopted` is a **description of existing code, written by reading it**: cover only
the surface your new work uses, record what it *actually* does including behavior you would not
have designed, and mark anything uncertain `unverified:`. An adopted contract is never frozen and
has no drift process — there is no promise to break. It is either accurate or wrong, and wrong
gets corrected against the source.

**Consistency pass — this is where integration bugs are prevented.** For every `depends_on`
edge, read both contracts side by side and confirm: the exported signature the consumer calls
exists with exactly that name and shape; the types match on both sides; every error the
provider raises is one the consumer's declared behavior accounts for; shared data shapes are
identical field for field, not merely similar. Fix mismatches now. This pass is cheap here and
expensive in Phase 4.

Over budget → the sub-module is too big. Split it, update the map, re-derive build order.

## Step 4 — Write `AGENTS.md`

Create `AGENTS.md` at the repository root if it does not exist. A short pointer, not a copy:
tell any agent opening this repo to read `_planning/MASTER_PROMPT.md` first and follow the phase
it routes to. This is what makes the rules reach a coding agent that never opens `_planning/` on
its own.

## Step 5 — Initialize `_state.md`

From the template. `phase: building`. Every sub-module `pending`, `attempts: 0`, in build order.

## Step 6 — Report and close

Commit.

Tell the user: sub-module count, build order, goal coverage confirmed complete, any sub-module you
split and why, and the first sub-module to build. Give them the exact invocation:

```
Follow _planning/MASTER_PROMPT.md — build <first-id>
```

Contracts are now frozen. From here they change only through `_drift.md`.

---

## PLAN exit checklist

- [ ] Every `❓` answered; new ones appended and left for Phase 2.5, not blocking
- [ ] **Goal coverage total** — no orphan criterion, no orphan sub-module
- [ ] `00_invariants.md` ≤ 250 lines; production parity filled; nothing restated from `STANDARDS.md`
- [ ] Dependency graph acyclic; `consumed_by` is the exact inverse of `depends_on`
- [ ] Every sub-module has a contract ≤ 150 lines
- [ ] Every contract carries `satisfies` with `constrained by:` lines copied verbatim
- [ ] Consistency pass done on every dependency edge
- [ ] No contract contains implementation guidance
- [ ] `owns` paths do not overlap between sub-modules
- [ ] Build order is a valid topological sort
- [ ] `AGENTS.md` exists at the repo root
- [ ] Every decision made without asking is in `_decisions.md`
- [ ] Zero lines of application code written; committed

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
6. If the ruling raises a new open decision, append a `❓` to `_incomplete.md` and let the router
   send it to Phase 2.5. Do not stop the project on it.
7. If the ruling changes what a `G-` criterion requires, update `00_goal.md` and re-check coverage.
8. Mark the drift entry `RESOLVED` with the date and what changed.
9. Commit.
10. Report: what changed, and the full list of now-`stale` sub-modules to rebuild.

Never resolve a drift entry that is still `OPEN`. **The user rules on drift, not you** — questions
are answered autonomously in this system, contract changes are not. A drift entry means a frozen
promise other sub-modules were built against cannot be kept, and those sub-modules are forbidden
to read the code that would reveal it.
