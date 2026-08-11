# Modular Supervised Build

A reusable, stack-agnostic prompt system for building an application across many small AI
coding sessions without losing quality, architectural consistency, or control of token cost.

## Use it

Copy `_planning/` into a new project, describe what you want, and point the agent at it:

```
Follow _planning/MASTER_PROMPT.md
```

The agent reads `_planning/_state.md`, works out which phase you are in, loads that one phase
protocol, and follows it. One folder, one invocation, every session.

## The four phases

| Phase | The agent does | You do |
|---|---|---|
| **1 Intake** | Splits the app into modules → sub-modules. Writes every open decision into `_incomplete.md`, nested under the module it belongs to. Writes no code. | Answer the `❓` inline, in the file. |
| **2 Plan** | Freezes `00_invariants.md`, the module map, and a contract for every sub-module — before any code exists. Checks every dependency edge for mismatches. Writes no code. | Nothing, unless a new question appears. |
| **3 Build** | One sub-module per session: implement to contract, write and run tests, conformance check, invariants self-review. | Answer anything new; rule on drift. |
| **4 Integrate** | Wires modules together, verifies declared contracts against real code, runs seam tests, security review. | Rule on any mismatch escalated as drift. |

Phases 1 and 2 run once. Phase 3 runs once per sub-module, each in a **fresh session**. Phase 4
runs once per module, then once for the whole app.

## How context survives between sessions

Each build session starts with no memory of the previous ones. What carries forward is a
**contract, not a history**.

A session building `billing.invoice-generator` never learns how `auth.session-store` was built.
It reads that sub-module's contract: exports and exact signatures, request/response shapes,
error names, env vars, persistent state touched, guarantees, non-goals. Roughly 40 lines, where
the implementation is 400 and is pure noise to a consumer.

Three properties follow:

**Contracts are declared during planning, not discovered during building.** Otherwise a
sub-module built early cannot know about one built later, so it guesses — and guessing is the
thing this system exists to prevent. Phase 2 writes every public surface up front. Build
sessions implement a frozen contract instead of inventing one.

**Per-session context is bounded by a sub-module's neighbor count, not by project size.** A
session loads the invariants, its own contract, and the contracts of its `depends_on` and
`consumed_by` — nothing else. A 12-sub-module project and an 80-sub-module project cost about
the same per session. That is the property that keeps token cost flat instead of growing.

**Everything is plain files in the repo.** They survive session death, diff in git, and you can
read and correct them directly. No conversation memory, no vector store, no nondeterministic
retrieval — the right contract is loaded because the map says which one, not because a search
ranked it highly.

## The two escape hatches

The system is built to stop rather than guess. Two files carry that:

**`_incomplete.md`** — an open decision nobody has made. Any phase can append to it. The
session stops until you answer inline.

**`_drift.md`** — a frozen contract that cannot be honored. The session files what was
declared, what reality requires, and the blast radius, then stops. You rule `APPROVED` or
`REJECTED`; Phase 2 amend mode applies the ruling and marks affected sub-modules `stale` so
they get rebuilt.

Neither is a failure mode. A session that stops with a precise question costs one round trip.
A session that guessed costs a debugging campaign at assembly.

## Guardrails worth knowing about

- **No borrowed decisions.** Common practice, "what most apps do", and "what seems safest" are
  all treated as assumptions. If it is not derivable from your stated requirements, it becomes
  a `❓`. The one exception is a choice with no observable consequence and no downstream
  dependency — genuinely internal detail is the build session's business.
- **Size budgets are decomposition signals.** A contract over 150 lines means the sub-module is
  too big and gets split. A build session over ~1200 lines of planning context means too many
  neighbors and gets a split or a facade. The budget catches bad boundaries early, cheaply.
- **Lane discipline.** A build session writes only inside its `owns` paths and never reads
  another sub-module's source. If a dependency looks wrong, that is drift — not something to
  quietly fix, because a silent fix breaks the next session's assumptions.
- **Three-part done bar.** A sub-module is `verified` only when contract conformance, its own
  tests, and the invariants self-review all pass. Tests passing with something outstanding is
  `built`, and the status field says so rather than rounding up.

## Layout

```
_planning/
  MASTER_PROMPT.md          router + the laws + the budgets — read every session
  system/
    01_intake.md            phase protocols, one loaded at a time
    02_plan.md
    03_build.md
    04_integrate.md
    templates/              blank forms for every runtime artifact
  00_invariants.md          ── created by Phase 2, loaded by every build session
  01_module_map.md          ── the tree, the edges, the build order
  02_contracts/<id>.md      ── one frozen public surface per sub-module
  _incomplete.md            ── ❓ questions, answered by you
  _drift.md                 ── contract change requests, ruled on by you
  _state.md                 ── per-sub-module status; drives routing
```

`system/` is the reusable part and is never edited during a build. Everything above it in the
tree is generated per project.
