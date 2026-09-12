# PHASE 3.5 — VERIFY

**Goal:** decide whether one `built` sub-module actually satisfies its contract, its goal
criteria, and the standards — judged by a session that did not build it.

**You are a judge. You do not write code.**

---

## Why this phase exists

A build session that grades its own work writes checks that confirm what the code does, not what
the contract requires. The reasoning that produced the code is also the reasoning that justifies
it. That is not a character flaw in the builder — it is what happens when the same context does
both jobs.

The fix is not a different model. It is a **session that never saw the building**. You have no
memory of the decisions, the dead ends, or the tradeoffs. You see only what was promised and what
exists. That is the whole mechanism.

Phase 3 gained a lot of freedom to decide things without asking. This phase is what pays for it.

---

## Step 1 — Load

Determine the target: the first sub-module with status `built` in `_state.md`.

Read exactly:

1. `_planning/system/STANDARDS.md`
2. The **Core goal** and the acceptance criteria this sub-module `satisfies` from
   `_planning/00_goal.md` — with their `proven by:` and `constrained by:` lines
3. `_planning/00_invariants.md`
4. `_planning/02_contracts/<target>.md`
5. The target's source and tests

**Never read:** the build session's transcript, its conformance table, its self-review, or its
commit messages beyond the file list. Inheriting the builder's reasoning reintroduces exactly the
problem this phase exists to remove. If a previous verdict exists for this target, read only its
findings list — not its reasoning.

**You never verify `adopted` code** — it was not built to a promise, so there is nothing to hold
it to. Judge only what this project built. You may read adopted source to check the new code uses
it correctly; if you find its contract describes it inaccurately, that is a finding against the
contract, not against the existing code.

## Step 2 — Judge against the contract

Go through the contract item by item — every export and signature, every route, method and
status, every error name, every env var, every declared side effect, every guarantee.

| Contract item | Declared | Found in code | Verdict |
|---|---|---|---|

**Read the code to answer this, not the tests.** A test can agree with a bug.

## Step 3 — Judge against the goal

For each acceptance criterion this sub-module satisfies:

- Does the code actually make that observable fact true?
- Does it honor the `constrained by:` line, or did it reach the same result a forbidden way?
- Run the `proven by:` check where it can be run at this level.

A sub-module can satisfy its contract perfectly and still fail its goal criterion. That is the
specific failure this step catches and no other step does.

## Step 4 — Scan for degradation

Check every item in `STANDARDS.md` §2 against the source:

- a mock, stub, fake or placeholder outside a test file
- a hardcoded value where config or an env var is required
- a `catch` that swallows
- `TODO`, `FIXME`, "for now", "temporary"
- **a test that asserts what the code does rather than what the contract requires** — read the
  assertions against the contract, not against the implementation
- a skipped or disabled test
- config that would have to change before deploy

Also check `STANDARDS.md` §3: the file-level feature comment, the what-not-how comment on each
function, single responsibility, dev/prod config parity.

## Step 5 — Run the tests yourself

Do not trust a reported result. Run them. Record what you actually observed.

Then ask the harder question: **do these tests test the contract, or the code?** A suite that
passes while asserting the implementation's accidents is worse than no suite, because it will
keep passing when the contract is violated.

## Step 6 — Rule

Write `_planning/03_verdicts/<target>.md` from `system/templates/verdict.md`.

**PASS** — contract conformance complete, every goal criterion satisfied, degradation scan clean,
tests pass and test the contract. Set status `verified` in `_state.md`.

**FAIL** — anything above unresolved. Set status `pending`, increment `attempts`, and write every
finding into the verdict file specifically enough that a fresh build session can act on it
without you.

**You must not fix anything.** Not the code, not the tests, not the contract — not even a missing
comment. The moment you edit, you own the code and you are grading yourself again, which is the
failure this phase exists to prevent. Every finding goes in the verdict file for a build session
to fix.

If the **contract itself** is wrong — it cannot satisfy the goal criterion it serves — that is
drift. File it in `_drift.md` and stop. Do not fail the sub-module for honoring a bad contract.

## Step 7 — Close

Update `_state.md`: status, `attempts`, and the verdict file that decided it.

Commit the verdict. **The commit must contain no source changes** — a diff touching code from a
verify session is a bug in how this phase was run.

Report: PASS or FAIL, the conformance table, goal criteria results, degradation scan, test results
as observed, and the next invocation.

---

## Verify exit checklist

- [ ] Build session's reasoning, conformance table and self-review never read
- [ ] Contract judged item by item against the **code**, not the tests
- [ ] Every goal criterion checked, including its `constrained by:` line
- [ ] Degradation scan run over the whole source
- [ ] Tests run by you, result recorded as observed
- [ ] Tests checked for asserting the contract rather than the implementation
- [ ] Verdict file written
- [ ] **No code, test, or contract file modified**
- [ ] `_state.md` updated with status and `attempts`, committed
