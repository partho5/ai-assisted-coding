# PHASE 3 — BUILD ONE SUB-MODULE

**Goal:** implement exactly one sub-module to its frozen contract, with tests, verified before
the session ends.

One sub-module per session. Never two.

---

## Step 1 — Load the slice

Determine the target: from the user's invocation, or the first `pending` or `stale` sub-module
in `_state.md` whose `depends_on` are all `verified`.

If the target is `stale`, a contract it depends on moved. Read the `RESOLVED` `_drift.md` entry
that caused it, and treat the changed contract as the current law — your existing code is the
thing that is out of date, not the contract.

Read **exactly these files and no others**:

1. `_planning/00_invariants.md`
2. `_planning/02_contracts/<target>.md` — your contract
3. `_planning/02_contracts/<n>.md` for every `n` in `depends_on` ∪ `consumed_by`
4. `_planning/_drift.md` — only entries touching those contracts
5. Your own `owns` source paths, if any already exist

**Do not read:** other sub-modules' source, the full module map beyond your own node,
`_incomplete.md`, or unrelated contracts. Reading a dependency's *implementation* is a Law 5
violation — its contract is the interface, and if the contract is insufficient that is a drift
entry, not a reason to go look.

If total planning context exceeds ~1200 lines, stop and file a drift entry proposing a split
or a facade. Too many neighbors is a decomposition bug.

Set your status to `in-progress` in `_state.md`.

## Step 2 — Reconcile before writing

Before the first line of code, check your contract against reality:

- Can every declared signature, error, and guarantee actually be implemented on this stack?
- Does every dependency contract give you what you need to honor yours?
- Does anything in `00_invariants.md` conflict with your contract?

Any "no" → **stop and file drift**. Do not start coding and hope.

Also check: does building this require a decision nobody has made — a policy, a limit, an
ordering rule, a user-visible behavior — that is not settled by your contract, the invariants,
or an answer? If so, append a `❓` to `_incomplete.md` under your sub-module, stop, and ask.

Only pure-internal choices with no observable consequence are yours to make. If a consumer
could ever notice it, it is not internal.

## Step 3 — Implement

Write only inside your `owns` paths.

- Implement the contract exactly: every declared export, signature, error, and guarantee.
  Nothing more. An undeclared export is scope creep and a future integration bug.
- Call dependencies **strictly as their contracts declare**. Not as you would design them,
  not as their name suggests.
- Follow `00_invariants.md` on naming, error model, logging, config, security, data access.
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

Run them. If they fail, fix the code. If they fail because the contract is wrong, file drift.

## Step 5 — Contract conformance check

Go through your contract item by item and verify the implementation against it. Produce a table:

| Contract item | Declared | Implemented | Match |
|---|---|---|---|

Cover every export name and signature, every route/method/status, every error name, every env
var, every declared side effect, every guarantee.

Any mismatch: fix the code, or — if the contract is genuinely wrong — file drift and stop.
Never edit your contract to match your code.

## Step 6 — Self-review against invariants

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

Fix everything marked FIX, then re-run tests.

## Step 7 — Close

Update `_state.md`:
- status → `verified` if Steps 4, 5, and 6 all pass; `built` if tests pass but something is
  outstanding (say what)
- record the files you created

Report to the user: what you built, the conformance table result, test results as actually
observed, any FIX items you resolved, and the next sub-module in build order with its exact
invocation.

Report failures plainly. A sub-module that does not pass is `built`, not `verified`, and
saying so is the whole point of having a status field.

---

## Build exit checklist

- [ ] Exactly one sub-module touched; all writes inside `owns`
- [ ] No other sub-module's source read or modified
- [ ] Every declared contract item implemented; nothing undeclared exported
- [ ] Dependencies called and stubbed exactly per their contracts
- [ ] Tests written, run, and passing
- [ ] Conformance table produced, no unresolved mismatch
- [ ] Invariants self-review complete, no unresolved FIX
- [ ] No assumption made — open decisions went to `_incomplete.md`, contract problems to `_drift.md`
- [ ] `_state.md` updated

---

## When to stop instead of finishing

Use `system/templates/_drift.md` when filing drift — create `_planning/_drift.md` from it if it
does not exist yet, otherwise append the next `D-<nnn>`. Append questions to the
"Appended during build" section of `_planning/_incomplete.md`. In both cases set your status
back to `pending` in `_state.md` and record the blocker under "Open blockers", so the next
session routes correctly instead of finding an orphaned `in-progress`.

Stop and hand back to the user when:

- the contract cannot be honored → `_drift.md`
- a dependency's contract is insufficient or contradicts yours → `_drift.md`
- context budget is blown → `_drift.md`, proposing a split or a facade
- a decision is required that nobody has made → `_incomplete.md`
- you need a dependency the invariants do not permit → `_incomplete.md`

In every case: revert your status to `pending`, record the blocker id under "Open blockers",
and leave no half-written source behind. The next session must find a clean starting point.

Stopping is a success state. A session that stopped with a precise question costs one round
trip. A session that guessed costs a debugging campaign at assembly.
