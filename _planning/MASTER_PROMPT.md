# MASTER PROMPT — Modular Supervised Build

You are running a project under a phased, contract-first build system. Everything you need
lives under `_planning/`. Follow this file first, every session.

**Invocation:** `Follow _planning/MASTER_PROMPT.md` — optionally with a target, e.g.
`Follow _planning/MASTER_PROMPT.md — build auth.session-store`.

---

## 1. Route

Read `_planning/_state.md`. If it does not exist, you are in Phase 1.

Check conditions in order; take the first that matches.

| # | Condition | Phase | Load and follow |
|---|---|---|---|
| 1 | No `_state.md` | 1 — Intake | `system/01_intake.md` |
| 2 | `_drift.md` has an `APPROVED` entry | 2 — Amend | `system/02_plan.md` (amend mode) |
| 3 | `_incomplete.md` has an unanswered `❓` | — | Stop. List them by id. Ask the user to answer inline in the file. |
| 4 | `_drift.md` has an `OPEN` entry | — | Stop. Summarize it and its blast radius. Ask the user to rule `APPROVED` or `REJECTED` in the file. |
| 5 | `phase: intake` | 2 — Plan | `system/02_plan.md` |
| 6 | `phase: building`, target given | 3 — Build | `system/03_build.md` |
| 7 | `phase: building`, no target | 3 — Build | pick the first `pending` or `stale` sub-module whose `depends_on` are all `verified`; then `system/03_build.md`. If every sub-module is `verified`, set `phase: integrating` and go to row 8. If none is eligible but some remain unbuilt, stop and report the deadlock — it means the graph or a status is wrong. |
| 8 | `phase: integrating` | 4 — Integrate | `system/04_integrate.md` |
| 9 | `phase: complete` | — | Report done. Ask what the user wants next. |

Rows 2–4 come before the phase rows deliberately: an unanswered question or an unresolved
contract change outranks whatever the phase field says, and it stops the project rather than
letting a session re-attempt the same wall. You never have to edit `_state.md` by hand to move
the project forward — answering the questions and ruling on drift is what advances it.

Load exactly one phase file. Do not read the others.

---

## 2. Non-negotiable laws

These bind in every phase. They are not preferences.

1. **No assumption.** If the answer is not in the project description or the planning files,
   it is a `❓`. Never infer a requirement, never fill a gap with something reasonable.
2. **No borrowed decisions.** Common practice, a convention from another project, or "what
   most apps do" is not evidence about *this* app. A default you did not derive from this
   project's stated requirements is an assumption wearing a disguise — make it a `❓`.
3. **Contracts are law.** Implement the declared contract exactly. If you cannot honor it,
   write a `_drift.md` entry and stop. Never silently deviate, extend, or "improve" a contract.
4. **Stay in your lane.** A build session writes only its own sub-module's source and tests.
   Never edit another sub-module's code, another contract, the module map, or the invariants.
5. **Read the slice, not the repo.** Load only the files your phase authorizes. Never read
   another sub-module's *source* during a build session — read its *contract*.
6. **Stop beats guess.** When blocked, write the question to the correct file and stop.
   Stopping is a success state, not a failure.
7. **Requirements, not tutorials.** Contracts and instructions state *what must be true*.
   They never contain step-by-step implementation walkthroughs or example implementations.
8. **Close the loop.** Every phase ends by updating `_state.md`. No exceptions.

---

## 3. Context budget

The whole point of this system is that per-session context stays flat as the project grows.
Enforce it:

| Artifact | Budget | If exceeded |
|---|---|---|
| `00_invariants.md` | 250 lines | Move detail into contracts. It is not a design doc. |
| One contract file | 150 lines | The sub-module is too big. Split it. |
| Build-session planning context (invariants + own contract + neighbor contracts) | ~1200 lines | Too many neighbors. File a drift entry proposing a split or a facade. |

A contract file exceeding budget is a decomposition bug, not a formatting problem. Treat it
as a signal, not an inconvenience.

---

## 4. Files

**System (reusable, never edited during a build):**

```
_planning/system/01_intake.md      Phase 1 protocol
_planning/system/02_plan.md        Phase 2 protocol (also amend mode)
_planning/system/03_build.md       Phase 3 protocol
_planning/system/04_integrate.md   Phase 4 protocol
_planning/system/templates/        Blank forms for every runtime artifact
```

**Runtime (created and updated by the phases):**

```
_planning/00_invariants.md         Global conventions. Loaded by every build session.
_planning/01_module_map.md         Module tree, dependencies, build order.
_planning/02_contracts/<id>.md     One frozen public surface per sub-module.
_planning/_incomplete.md           ❓ questions, answered inline by the user.
_planning/_drift.md                Contract change requests.
_planning/_state.md                Per-sub-module status. Source of truth for routing.
```

---

## 5. Sub-module IDs

Format: `<module>.<sub-module>`, kebab-case, e.g. `auth.session-store`,
`billing.invoice-generator`. An ID is stable for the life of the project and is the key used
in the map, the contract filename, and `_state.md`.

---

## 6. Statuses

`pending` → `in-progress` → `built` → `verified`

Plus `stale` — a contract this sub-module depends on changed after it was built. A `stale`
sub-module must be re-run through Phase 3 before integration.

---

Now load the phase file your route selected and follow it.
