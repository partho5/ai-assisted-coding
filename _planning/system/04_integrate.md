# PHASE 4 — INTEGRATE

**Goal:** wire verified sub-modules together and prove the seams hold in real code.

This is the only phase that reads implementation source across sub-module boundaries. Build
sessions trust contracts; this session checks whether that trust was warranted.

---

## Step 0 — Gate

Every sub-module in `_state.md` must be `verified`. Any `pending`, `in-progress`, `built`, or
`stale` entry → stop and report which. `stale` in particular means a contract moved under a
finished sub-module; rebuild it through Phase 3 first.

Every `_drift.md` entry must be `RESOLVED` or `REJECTED`. An `OPEN` or `APPROVED` entry means
the plan is mid-change — stop.

## Step 1 — Pick a scope

Integrate bottom-up, one scope per session. Never the whole app at once.

1. **Module scope** — one module's sub-modules plus their direct external edges.
   Run these first, in the same leaf-first order as the build.
2. **Application scope** — the top-level wiring: entry point, routing, dependency wiring,
   configuration, startup and shutdown. Run last, once every module scope passes.

Record which scope you are in; update `_state.md` with an `integration` section as you go.

## Step 2 — Load

- `_planning/system/STANDARDS.md`
- `_planning/00_goal.md` — all of it. At application scope you are proving this file true.
- `_planning/00_invariants.md`
- `_planning/01_module_map.md` — the nodes in scope, and the coverage table
- contracts for every sub-module in scope
- **the public surface source** of those sub-modules — the exported entry points and type
  definitions. Not their internals. If you find yourself reading a private helper to understand
  a seam, the contract was under-specified: note it as a finding.

Stay within budget. If a scope will not fit, split the scope, not the rule.

## Step 3 — Verify contracts against reality

For every dependency edge in scope, compare **declared contract** to **actual code** on both sides:

| Edge | Contract says | Provider actually | Consumer actually calls | Match |
|---|---|---|---|---|

Check names, arity, parameter and return types, error names and conditions, nullability,
status codes, payload field names, and declared guarantees.

A mismatch here is a real defect and one of two things:

- **Code diverged from contract** → fix the code, inside that sub-module's `owns` paths only.
  Re-run that sub-module's tests. Note it; a divergence means a Phase 3 conformance check
  passed when it should not have.
- **Contract was wrong** → `_drift.md`, stop, and take it through Phase 2 amend mode.

Never adjust a contract from this phase to make a mismatch disappear.

## Step 4 — Wire

Write only the integration code the scope requires: composition, routing registration,
dependency wiring, configuration assembly, startup/shutdown ordering.

- Follow `00_invariants.md` exactly, as a build session would.
- Add no behavior that belongs inside a sub-module. If wiring needs logic, that logic is a
  missing sub-module — file drift rather than smuggling it into the glue.
- Add no undeclared coupling. If two sub-modules need to talk and no edge exists in the map,
  that is a planning gap → `_drift.md`.

## Step 5 — Seam tests

Write integration tests that exercise real components across the seam — **no stubs between
sub-modules in scope**. Stubs are permitted only at the true system edge (third-party services,
network, clock, randomness) and must match those declared contracts.

Cover per seam:
- the primary path through the boundary
- each declared error propagating across it correctly, with its declared identity intact
- the declared guarantees holding end to end (ordering, idempotency, atomicity, transactions)
- configuration and startup actually producing a working wiring

Run them. Run the full suite, not just the new tests — cross-module wiring is exactly where
previously passing tests break.

## Step 6 — Ground truth: does it run, and does it meet the goal

**Application scope only.** Everything before this proves that parts match their descriptions.
This step is the only one that proves the system exists.

### 6a — Smoke boot

Start the system the way it will actually be started, using the run command in the invariants,
with production-equivalent configuration. Send it **one real request** through its real entry
point and observe a real response.

A full suite of green unit tests on an application that does not boot is the classic way an
autonomous build reports success on nothing. This step is what makes that impossible.

Record the command and the observed output. If it does not start, nothing below matters — stop
and report that.

### 6b — Execute every acceptance criterion

For each `G-` criterion in `00_goal.md`, run its `proven by:` check against the running system.

| Criterion | `proven by:` | Observed | Verdict |
|---|---|---|---|

Then write the result back into `00_goal.md`, setting each criterion's `status:` to `proven` or
leaving it `unproven` with the reason.

A criterion that cannot be proven is not a documentation problem. Either the system does not do
what the project was for, or the criterion was never checkable and Phase 0 let it through. Say
which. **`phase: complete` requires every criterion `proven`.**

Also re-check goal coverage against the map: a criterion nobody satisfies, discovered here, means
the plan was frozen with a hole in it.

### 6c — Whole-system review

Review against `00_invariants.md` and `STANDARDS.md` end to end:

- **security** — authn/authz enforced on every externally reachable path, not merely available;
  every external input validated at its entry point; no secret in logs, errors, or responses;
  configuration and secrets loaded per the invariants; no debug or bypass path left reachable
- error handling — every path has a declared failure mode; nothing leaks an internal error to
  an external caller
- logging present per the invariants
- no orphan: every sub-module in the map is actually reachable from the entry point
- **`STANDARDS.md` §2 degradation scan across the whole codebase**, including the wiring you just
  wrote — no mock or placeholder outside test files, no hardcoded value where config belongs, no
  `TODO` counted as done, no skipped test
- **production parity** — the invariants name exactly which values change at deploy. Confirm
  nothing else would have to. Anything beyond credentials and environment-specific values is a
  defect to fix here, not a deployment note.
- lint, format, build, and full test suite run clean, using the exact commands in the invariants

## Step 7 — Close

Update `_state.md`: scope marked integrated; `phase: integrating` until application scope passes,
then `phase: complete` — **only if every acceptance criterion is `proven`**.

Commit.

Report: the verification table, the smoke boot result, the acceptance criteria table, every
mismatch found and how it was resolved, test results as actually observed, anything left
outstanding, and the next scope with its invocation.

Two numbers are the feedback signal on how well the earlier phases are working — report both
plainly, and do not round either down:

- **contract-vs-code mismatches**, and in which sub-modules. These are sub-modules that passed
  Phase 3.5 when they should not have.
- **acceptance criteria that came out unproven.** These are the ones that matter most: a project
  can pass every other check in this system and still fail here, which is the entire reason this
  step exists.

---

## Integrate exit checklist

- [ ] All in-scope sub-modules `verified`, no `stale`, no open drift
- [ ] Every edge in scope verified contract-vs-code, table produced
- [ ] Mismatches fixed in code, or escalated to drift — never papered over by editing a contract
- [ ] Integration code contains wiring only, no smuggled business logic
- [ ] Seam tests use real components across in-scope boundaries
- [ ] Full test suite run and passing
- [ ] **System boots with production-equivalent config and serves one real request** (app scope)
- [ ] **Every acceptance criterion executed, result written back into `00_goal.md`** (app scope)
- [ ] Security review done at application scope
- [ ] Degradation scan clean across the whole codebase, wiring included
- [ ] Production parity confirmed — only credentials and env values change at deploy
- [ ] `phase: complete` set only if every criterion is `proven`
- [ ] `_state.md` updated, committed
