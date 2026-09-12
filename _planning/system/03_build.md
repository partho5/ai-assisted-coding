# PHASE 3 — BUILD ONE SUB-MODULE

**Goal:** implement exactly one sub-module to its frozen contract, with tests, ready to be judged.

One sub-module per session. Never two.

**Asking posture: almost none.** Everything the product needed to decide was decided in Phase 0
and written into `00_goal.md`. Everything technical was decided in Phase 2 and frozen into your
contract and the invariants. `STANDARDS.md` §1 binds fully here — decide the never-ask items and
log them, derive the rest from the goal, and stop only for the always-ask list or contract drift.
A build session that stops to ask about naming, file layout, or a helper function is the failure
this system was rebuilt to remove.

**You cannot mark your own work `verified`.** The best status this session can reach is `built`.
A Phase 3.5 session that never saw you work decides the rest.

---

## Step 1 — Load the slice

Determine the target: from the user's invocation, or the first `pending` or `stale` sub-module
in `_state.md` whose `depends_on` are all `verified` or `adopted`.

If the target is `stale`, a contract it depends on moved. Read the `RESOLVED` `_drift.md` entry
that caused it, and treat the changed contract as the current law — your existing code is the
thing that is out of date, not the contract.

Read **exactly these files and no others**:

1. `_planning/system/STANDARDS.md`
2. The **Core goal** of `_planning/00_goal.md`, plus the criteria your contract `satisfies` —
   with their `constrained by:` lines
3. `_planning/00_invariants.md`
4. `_planning/02_contracts/<target>.md` — your contract
5. `_planning/02_contracts/<n>.md` for every `n` in `depends_on` ∪ `consumed_by`
6. `_planning/03_verdicts/<target>.md` — **if it exists**, a previous verify failed this
   sub-module. Read its **Findings** section and fix every item. Do not read its reasoning.
7. `_planning/_decisions.md` — so you stay consistent with decisions already made
8. `_planning/_drift.md` — only entries touching those contracts
9. Your own `owns` source paths, if any already exist

**Do not read:** the source of a `kind: declared` sub-module, the module map beyond your own node,
`_incomplete.md`, or unrelated contracts. For those the contract *is* the interface; if it is
insufficient that is drift, not a reason to go look.

**Do read, whenever you need certainty:** the real source behind any `kind: adopted` dependency,
and any file in your `extends` list. That code is a fact, not a promise (Law 8), and its contract
is only a cache. **If the source disagrees with it, the source wins** — correct the contract, note
it in your report, carry on. That is not drift and does not stop you.

If total planning context exceeds ~1200 lines, stop and file a drift entry proposing a split
or a facade. Too many neighbors is a decomposition bug.

Set your status to `in-progress` in `_state.md`.

## Step 2 — Reconcile before writing

Before the first line of code, check your contract against reality:

- Can every declared signature, error, and guarantee actually be implemented on this stack?
- Does every dependency contract give you what you need to honor yours?
- Does anything in `00_invariants.md` conflict with your contract?

Any "no" → **stop and file drift**. Do not start coding and hope.

Also check your contract against the goal criteria it `satisfies`: can implementing this contract
actually make those observable facts true? If not, **that is drift** — the goal outranks the
contract (Law 3). File it rather than building a contract that provably cannot deliver.

### Before you write a `❓`

Run `STANDARDS.md` §1 in this order. Most build-session questions die at step 1 or 2.

1. **On the never-ask list?** Naming, file layout, internal signatures, a standard library for a
   solved problem, log wording, test naming, code style, a helper, field ordering, internal
   timeouts, error text no user sees → **decide it.** Log one line in `_decisions.md`. Do not ask.
2. **Does the goal settle it?** Check the core goal, your criteria's `constrained by:` lines, the
   ranked intent, and the "when in doubt" rules. If two options both work, the ranking decides —
   that is what it is for. → **decide it,** log the derivation.
3. **On the always-ask list?** Which LLM, which database, auth model, a persisted schema,
   anything costing money per use, anything a user sees and judges, a permanent external
   dependency → **`❓`, always.**
4. **Otherwise:** ask only if it is **expensive or impossible to change later**. Cheap to
   reverse → decide it and log it.

A question you could have answered from the goal is a stop you charged the user for. A decision
you made without logging it is an assumption nobody can audit. Both are failures; the log is what
makes the first one safe.

## Step 3 — Implement

Write inside your `owns` paths, and at the **named touch-points** in your `extends` list —
nothing else in those files. In an `extends` file you may not restructure, rename, reformat or
refactor anything, not even something obviously wrong: other code depends on it and nothing here
is checking that. Report it instead.

- Implement the contract exactly: every declared export, signature, error, and guarantee.
  Nothing more. An undeclared export is scope creep and a future integration bug.
- Call dependencies **strictly as their contracts declare**. Not as you would design them,
  not as their name suggests.
- Follow `00_invariants.md` on naming, error model, logging, config, security, data access.
- Follow `STANDARDS.md` §3 on every file you write: a feature comment at the top of the file, a
  what-not-how comment on every function, one responsibility per function, dev config equal to
  prod config.
- **Honor the `constrained by:` line of every criterion you satisfy.** Reaching the same
  observable result a different way still fails — the constraint is part of the requirement, not
  advice.
- **Never degrade to proceed** (`STANDARDS.md` §2). No mock or placeholder outside a test file,
  no hardcoded value where config belongs, no swallowed `catch`, no `TODO` counted as done, no
  skipped test, no config that must change before deploy. Blocked means drift or a `❓` — never
  a convenient substitute.
- Add no dependency that is not permitted by the invariants. Needing one is a `❓`.
- Handle every error condition your contract declares. Handle the boundary cases the invariants
  and your contract specify. Validate every input crossing your public surface.
- Never modify another sub-module's files. If a dependency looks wrong, that is drift — you do
  not have the authority to fix it, and fixing it silently breaks the next session.

## Step 4 — Tests

Written in this session, in the location and framework the invariants specify. They must pass
before you finish.

Cover:
- the happy path of every declared export
- every error condition your contract declares, asserted by its declared name/code
- input validation and boundary behavior at your public surface
- the guarantees you declared (idempotency, ordering, atomicity — whichever apply)

Do not test other sub-modules. **Stub dependencies strictly to their declared contracts** —
same signatures, same return shapes, same error names. A stub that behaves more conveniently
than the real contract is how integration bugs are manufactured; it makes your tests pass and
the assembled system fail.

**Assert what the contract requires, not what your code does.** A test written by reading your own
implementation will keep passing when the implementation is wrong, which is worse than no test.
Write each assertion from the contract line it checks. Phase 3.5 reads your assertions against the
contract specifically to catch this.

Run them. If they fail, fix the code. If they fail because the contract is wrong, file drift.

Also run the project's **existing** tests. A change that breaks what already worked is not done
(`STANDARDS.md` §3).

## Step 5 — Contract conformance pre-flight

> **Steps 5 and 6 are a pre-flight, not a grade.** You are catching your own obvious misses
> cheaply, before a verify session spends a whole session on them. Passing them does not make the
> sub-module correct and does not make it `verified`. Do not write them up as proof of anything.

Go through your contract item by item and verify the implementation against it. Produce a table:

| Contract item | Declared | Implemented | Match |
|---|---|---|---|

Cover every export name and signature, every route/method/status, every error name, every env
var, every declared side effect, every guarantee.

Any mismatch: fix the code, or — if the contract is genuinely wrong — file drift and stop.
Never edit your contract to match your code.

## Step 6 — Pre-flight against invariants and standards

Re-read your output against `00_invariants.md` and confirm each, listing PASS or FIX:

- naming conventions — files, functions, types, DB objects, routes, env vars
- error model — representation, propagation, what surfaces to the caller
- logging — required events present; nothing forbidden is logged
- **security** — every public input validated; authz enforced where the invariants require it;
  no secret in logs, errors, or responses; no injection via string-built queries or commands;
  output encoded per the invariants; no credential or key hardcoded
- configuration read per the invariants, never inline
- no undeclared dependency added
- lint/format/build commands from the invariants run clean

Then `STANDARDS.md`:

- **§2 degradation scan** — grep your own output for `TODO`, `FIXME`, "for now", "temporary",
  mocks or placeholders outside test files, hardcoded values that belong in config, swallowed
  `catch` blocks, skipped tests
- **§3 code rules** — feature comment on every file, what-not-how comment on every function,
  single responsibility, dev config equals prod config
- every `constrained by:` line honored, not just the observable result reached

Fix everything marked FIX, then re-run tests.

## Step 7 — Close

Update `_state.md`:
- status → **`built`**. Never `verified` — only a Phase 3.5 session may set that (Law 6).
- record the files you created
- if a verdict sent you here, note which findings you fixed

**Commit** (`STANDARDS.md` §4).

Report to the user: what you built, the pre-flight results, test results as actually observed,
every decision you made without asking and what it derives from, and the next invocation — which
is a **verify** session for this sub-module, not the next build:

```
Follow _planning/MASTER_PROMPT.md
```

Report failures plainly. Tests passing with something outstanding is still `built` with the
outstanding item named, and saying so is the whole point of having a status field.

---

## Build exit checklist

- [ ] Exactly one sub-module touched; all writes inside `owns`
- [ ] No other sub-module's source read or modified
- [ ] Every declared contract item implemented; nothing undeclared exported
- [ ] Every `constrained by:` line honored, not just the result reached
- [ ] Dependencies called and stubbed exactly per their contracts
- [ ] Tests written from the contract, not from the implementation; run and passing
- [ ] Existing tests still passing
- [ ] Conformance pre-flight produced, no unresolved mismatch
- [ ] Invariants and standards pre-flight complete, no unresolved FIX
- [ ] Degradation scan clean — no TODO, mock, placeholder, hardcoded config, or skipped test
- [ ] Every decision made without asking is logged in `_decisions.md` with its derivation
- [ ] Nothing asked that the never-ask list or the goal already settled
- [ ] Status set to `built` — **not** `verified`
- [ ] `_state.md` updated, committed
- [ ] Prior verdict findings, if any, all addressed

---

## When to stop instead of finishing

| Situation | File it as |
|---|---|
| the contract cannot be honored | `_drift.md` |
| the contract cannot satisfy a `G-` criterion it claims | `_drift.md` |
| a dependency's contract is insufficient or contradicts yours | `_drift.md` |
| context budget blown | `_drift.md`, proposing a split or a facade |
| a decision on the **always-ask list** is required | `_incomplete.md` |
| you need a dependency the invariants do not permit | `_incomplete.md` |

Drift: append the next `D-<nnn>` using `system/templates/_drift.md`, and list it under "Open
blockers". Questions: append to "Appended during build" in `_incomplete.md` — **not** a blocker,
Phase 2.5 sees it first and only lists it if it escalates.

Either way: revert to `pending` (never leave yourself `built` — that routes straight to verify)
and leave no half-written source behind.

A `❓` does not halt the project; drift does. A frozen promise other sub-modules were built
against is not yours to change.
