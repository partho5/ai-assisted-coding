# MASTER PROMPT — Modular Supervised Build (v2)

You are running a project under a phased, contract-first build system. Everything you need lives
under `_planning/`. Follow this file first, every session.

**Invocation:** `Follow _planning/MASTER_PROMPT.md` — optionally with a target, e.g.
`Follow _planning/MASTER_PROMPT.md — build auth.session-store`.

**Load before anything else, every session, every phase:**

1. `_planning/system/STANDARDS.md`
2. The **Core goal** section of `_planning/00_goal.md`, if it exists

---

## 1. Route

Read `_planning/_state.md`. Check conditions in order; take the first that matches.

| # | Condition | Phase | Load and follow |
|---|---|---|---|
| 1 | No `_planning/00_goal.md` | 0 — Understand | `system/00_understand.md` |
| 2 | No `_state.md` | 1 — Modules | `system/01_modules.md` |
| 3 | Any sub-module has `attempts` ≥ 3 | — | Stop. Report it with every verdict so far. Three failed builds is a contract or a decomposition problem, not a coding problem. |
| 4 | `_drift.md` has an `APPROVED` entry | 2 — Amend | `system/02_plan.md` (amend mode) |
| 5 | `_incomplete.md` has an unanswered `❓` | 2.5 — Resolve | `system/02b_resolve.md` |
| 6 | `_drift.md` has an `OPEN` entry | — | Stop. Summarize it and its blast radius. Ask the user to rule `APPROVED` or `REJECTED` in the file. |
| 7 | `phase: understood` | 1 — Modules | `system/01_modules.md` |
| 8 | `phase: modules` | 2 — Plan | `system/02_plan.md` |
| 9 | `phase: building` and any sub-module is `built` | 3.5 — Verify | `system/03b_verify.md` |
| 10 | `phase: building`, target given | 3 — Build | `system/03_build.md` |
| 11 | `phase: building`, no target | 3 — Build | pick the first `pending` or `stale` sub-module whose `depends_on` are all `verified` or `adopted`; then `system/03_build.md`. If every sub-module is `verified` or `adopted`, set `phase: integrating` and go to row 12. If none is eligible but some remain unbuilt, stop and report the deadlock — the graph or a status is wrong. |
| 12 | `phase: integrating` | 4 — Integrate | `system/04_integrate.md` |
| 13 | `phase: complete` | — | Report done. Ask what the user wants next. |

Order matters: row 5 resolves questions instead of halting; row 9 verifies a `built` sub-module
before any new one starts, so a failure is caught before anything is built on top of it.

Load exactly one phase file. Do not read the others.

---

## 2. Non-negotiable laws

These bind in every phase. They are not preferences.

1. **Ask at the start, not at the end.** Phase 0 asks maximally and is never automated. Phase 2
   collects questions without stopping. By Phase 3 you ask almost nothing.
2. **Decide or escalate — never assume silently.** From Phase 1 on, `STANDARDS.md` §1 says what
   you may decide alone. Record every such decision in `_decisions.md`; an unrecorded decision is
   an assumption. Phase 0 is exempt — it is the interview.
3. **The goal outranks the contract.** A contract that cannot satisfy the criterion it claims is
   drift, not something to implement faithfully.
4. **Contracts are law.** Implement a `declared` contract exactly. Cannot honor it → `_drift.md`,
   stop. Never silently deviate, extend, or improve one.
5. **Never degrade to proceed.** `STANDARDS.md` §2 lists what counts. Blocked means drift or a
   `❓` — never a convenient substitute.
6. **You do not grade your own work.** A build session reaches `built`. Only Phase 3.5, which
   never saw the building, awards `verified`.
7. **Stay in your lane.** Write only your own sub-module's source and tests, plus the named
   touch-points in `extends`. Never edit another sub-module's code, another contract, the map, or
   the invariants.
8. **Read the slice, not the repo — but never guess about code that already exists.** A
   `declared` sub-module is read through its contract, never its source: the contract is a
   promise, and coupling to its accidents breaks the next rebuild. **Existing code is a fact, not
   a promise — read it whenever you need certainty.** Its contract is only a cache; if they
   disagree, **the source wins**. Saving tokens is never a reason to build the wrong thing.
9. **Stop beats guess.** Blocked on something the goal cannot settle and the lists say to
   escalate → write it to the right file and stop. Stopping is a success state.
10. **Requirements, not tutorials.** State what must be true, never how to build it. A
    `constrained by:` line is one line of constraint, not a recipe.
11. **Close the loop.** Every phase updates `_state.md` and commits. No exceptions.

---

## 3. Context budget

Per-session context stays flat as the project grows. Enforce it:

| Artifact | Budget | If exceeded |
|---|---|---|
| `system/STANDARDS.md` | 60 lines | A rule that serves one phase belongs in that phase file. |
| `00_goal.md` | 120 lines | It is decision fuel, not a description. Detail goes to `00_brief.md`. |
| `00_brief.md` | 150 lines | Not loaded by build sessions, but a brief this long is unread. |
| `00_invariants.md` | 250 lines | Move detail into contracts. It is not a design doc. |
| One contract file | 150 lines | The sub-module is too big. Split it. |
| Build-session context (standards + core goal + own criteria + invariants + own contract + neighbor contracts) | ~1200 lines | Too many neighbors. File drift proposing a split or a facade. |

A contract file over budget is a decomposition bug, not a formatting problem.

---

## 4. Files

```
system/            VERSION, STANDARDS.md, one protocol per phase, templates/ — never edited
00_inputs/         raw material you drop in, any format
00_brief.md        non-technical description          00_goal.md      criteria + ranked intent
00_invariants.md   technical conventions              01_module_map.md  tree, edges, coverage
02_contracts/<id>.md   one surface per sub-module     03_verdicts/<id>.md   verify results
_incomplete.md  ❓   _decisions.md  what was decided alone   _drift.md  contract changes
_state.md          status per sub-module — source of truth for routing
```

---

## 5. Sub-module IDs

Format: `<module>.<sub-module>`, kebab-case, e.g. `auth.session-store`,
`billing.invoice-generator`. An ID is stable for the life of the project and is the key used in
the map, the contract filename, the verdict filename, and `_state.md`.

---

## 6. Statuses

`pending` → `in-progress` → `built` → `verified`

`built` — implemented, tests pass, the build session's own pre-flight passed. **Not confirmed.**
`verified` — a Phase 3.5 session that never saw the building has passed it. **Only Phase 3.5 may
set this.**

`stale` — a contract this sub-module depends on changed after it was built. Must re-run Phase 3.

`adopted` — existing code this system did not build. Its contract *describes* it instead of
promising anything, so it is never `built` and never `verified` — there is no promise to check it
against. Build order treats `adopted` as already satisfied.

---

## 7. Existing projects

The phases and this command do not change when a project already exists. One rule covers it:

**If an artifact already exists, reuse it. If it does not and code exists, read the code.**

- `00_goal.md`, `00_invariants.md`, contracts already there → they stand. Plan only the new work.
  Do not re-interview, re-decide, renumber, or reset finished sub-modules.
- Code exists but those files do not → **read the source to write them.** Invariants are
  *extracted* from the code, not decided. Where the code is inconsistent with itself, record both
  patterns and raise a `❓`.
- An existing component your new work depends on becomes a normal `depends_on` entry with status
  `adopted` and a contract written by reading it.
- Coverage is checked only against this round's criteria. Earlier ones are already satisfied by
  code that exists.

`attempts: <n>` — incremented on every verify FAIL. At 3, the project stops (row 3).

---

Now load the phase file your route selected and follow it.
