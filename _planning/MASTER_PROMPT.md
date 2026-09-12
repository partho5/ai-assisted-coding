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
| 11 | `phase: building`, no target | 3 — Build | pick the first `pending` or `stale` sub-module whose `depends_on` are all `verified`; then `system/03_build.md`. If every sub-module is `verified`, set `phase: integrating` and go to row 12. If none is eligible but some remain unbuilt, stop and report the deadlock — the graph or a status is wrong. |
| 12 | `phase: integrating` | 4 — Integrate | `system/04_integrate.md` |
| 13 | `phase: complete` | — | Report done. Ask what the user wants next. |

Two orderings are deliberate:

**Row 5 resolves instead of stopping.** In v1 an open question halted the project. Now it routes
to a session that answers what it can from the goal and stops **once**, with only what genuinely
needs you. This is the change that removes most interruptions.

**Row 9 sits above rows 10 and 11.** A `built` sub-module gets verified before any new one starts,
so a failure is caught before anything is built on top of it.

Rows 7 and 8 are separate on purpose. Phase 0 writes `_state.md`, so row 2 no longer catches a
project that has understood but not decomposed — `phase: understood` must route to Phase 1
explicitly or module decomposition is skipped.

Load exactly one phase file. Do not read the others.

---

## 2. Non-negotiable laws

These bind in every phase. They are not preferences.

1. **Ask at the start, not at the end.** The asking budget inverts across the run. Phase 0 asks
   maximally and is never automated. Phase 2 collects questions without stopping. By Phase 3 you
   ask almost nothing. Under-asking in Phase 0 is what causes over-asking later.
2. **Decide or escalate — never assume silently.** From Phase 1 onward, `STANDARDS.md` §1 governs
   what you may decide alone. Decide what is on the never-ask list, derive what the goal settles,
   escalate what is on the always-ask list. Record every decision in `_planning/_decisions.md`.
   An unrecorded decision is an assumption. **Phase 0 is exempt** — it is the interview.
3. **The goal outranks the contract.** A contract that conflicts with an acceptance criterion in
   `00_goal.md` is drift. Do not implement a contract that cannot satisfy the goal it serves.
4. **Contracts are law.** Implement the declared contract exactly. If you cannot honor it, write
   a `_drift.md` entry and stop. Never silently deviate, extend, or "improve" a contract.
5. **Never degrade to proceed.** `STANDARDS.md` §2 lists what counts. Blocked means file drift or
   raise a `❓` — never substitute something easier and continue.
6. **You do not grade your own work.** A build session can reach `built`. Only a Phase 3.5 verify
   session, which never saw the building, can award `verified`.
7. **Stay in your lane.** A build session writes only its own sub-module's source and tests.
   Never edit another sub-module's code, another contract, the module map, or the invariants.
8. **Read the slice, not the repo.** Load only the files your phase authorizes. Never read
   another sub-module's *source* during a build session — read its *contract*.
9. **Stop beats guess.** When blocked on something the goal cannot settle and the lists say to
   escalate, write it to the correct file and stop. Stopping is a success state.
10. **Requirements, not tutorials.** Contracts and instructions state *what must be true*. They
    never contain step-by-step walkthroughs or example implementations. A `constrained by:` line
    is one line of constraint, not a recipe.
11. **Close the loop.** Every phase ends by updating `_state.md` and committing. No exceptions.

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

**System (reusable, never edited during a build):**

```
_planning/system/VERSION              2
_planning/system/STANDARDS.md         Loaded every session, every phase
_planning/system/00_understand.md     Phase 0 protocol
_planning/system/01_modules.md        Phase 1 protocol
_planning/system/02_plan.md           Phase 2 protocol (also amend mode)
_planning/system/02b_resolve.md       Phase 2.5 protocol
_planning/system/03_build.md          Phase 3 protocol
_planning/system/03b_verify.md        Phase 3.5 protocol
_planning/system/04_integrate.md      Phase 4 protocol
_planning/system/templates/           Blank forms for every runtime artifact
```

**Runtime (created and updated by the phases):**

```
_planning/00_inputs/                  Raw material you drop in. Any format.
_planning/00_brief.md                 Non-technical description. A product person can check it.
_planning/00_goal.md                  Core goal, acceptance criteria, ranked intent. Decision fuel.
_planning/00_invariants.md            Technical conventions. Loaded by every build session.
_planning/01_module_map.md            Module tree, dependencies, goal coverage, build order.
_planning/02_contracts/<id>.md        One frozen public surface per sub-module.
_planning/03_verdicts/<id>.md         Verify results. Read by the next build session on a FAIL.
_planning/_incomplete.md              ❓ questions. Answered by Phase 2.5 or by you.
_planning/_decisions.md               Every decision made without asking, and what it derives from.
_planning/_drift.md                   Contract change requests.
_planning/_state.md                   Per-sub-module status. Source of truth for routing.
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

`attempts: <n>` — incremented on every verify FAIL. At 3, the project stops (row 3).

---

Now load the phase file your route selected and follow it.
