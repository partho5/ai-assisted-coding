# Modular Supervised Build

A reusable, stack-agnostic prompt system for building an application across many small AI coding
sessions without losing quality, architectural consistency, or control of token cost.

It is a folder of Markdown files. There is nothing to install and no code to run.

---

## How to use it

**1. Get the files into your project.**

```bash
git clone <this repo> && cp -r project-planning/_planning your-project/
cd your-project
```

**2. Drop in whatever you already have.**

```
_planning/00_inputs/
```

Notes, a spec, a spreadsheet, screenshots, a PDF, a half-written README — any format, any mess.
This is the front door. You do not need to write a clean project description first; producing one
is the system's first job.

**3. Run the same command, in a fresh session, every time.**

```
Follow _planning/MASTER_PROMPT.md
```

That is the whole interface. The agent reads `_planning/_state.md`, works out which phase the
project is in, loads that one phase protocol, and follows it. You never pick the phase.

Run it again for the next step. Keep running it until the project is done.

---

## What happens, in order

| Phase | What the agent does | What you do |
|---|---|---|
| **0 Understand** | Reads everything in `00_inputs/` and **interviews you properly**. Writes `00_brief.md` — a plain-language description a product person can check — and `00_goal.md`, the goal, the acceptance criteria, and a ranked list of what matters. | Answer. This phase is a real conversation and it is the one that matters most. |
| **1 Modules** | Splits the app into modules → sub-modules, in plain language. Ties every sub-module to the goal criteria it serves. | Nothing, usually. |
| **2 Contracts** | Freezes the invariants, the module map, and a contract for every sub-module — before any code exists. Collects technical questions without stopping. | Nothing. |
| **2.5 Resolve** | Answers every question the goal already settles. Stops **once** with only what genuinely needs you — with options weighed and a recommendation. | Answer a short batch. Rarely. |
| **3 Build** | One sub-module per session: implement to contract, write tests, commit. Asks almost nothing. | Nothing. |
| **3.5 Verify** | A **fresh session that never saw the building** judges it against the contract, the goal, and the standards. | Nothing. |
| **4 Integrate** | Wires modules together, proves the system boots, and runs every acceptance criterion against the real app. | Rule on any contract change. |

Phases 0–2 run once. Phase 3 and 3.5 run once per sub-module, each in a **fresh session**.
Phase 4 runs once per module, then once for the whole app.

---

## The four ideas it is built on

### 1. The asking budget inverts

Planning asks a lot. Building asks almost nothing.

Phase 0 is deliberately **not automated** — it interviews you until nothing about the product is
ambiguous. By Phase 3 the agent should be asking you essentially nothing, because everything was
already settled and written down somewhere it can read.

This is the fix for the most common complaint about v1: *the agent stops constantly to ask
obvious things.* It did that because there was no goal file — "derivable from the requirements"
meant derivable from one paragraph, so nearly everything became a question. Now there is a file
rich enough to answer them.

Two explicit lists in `system/STANDARDS.md` do the rest. A vague instruction like "use your
judgment" changes nothing; a list does:

| Never ask — decide and log one line | Always ask |
|---|---|
| names, folders, file layout | which LLM |
| a standard library for a solved problem | which database |
| log wording, test file naming | auth model |
| code style, helper functions | a persisted data schema |
| internal timeouts and retries | anything costing money per use |
| error text no user sees | anything a user sees and judges |

Anything else gets asked only if it is **expensive to reverse** *and* the goal cannot settle it.
Every decision made without asking is written to `_decisions.md` with what it derives from — an
unrecorded decision is just an assumption.

### 2. Intent is written as a ranking, not a description

This is what lets the agent decide instead of asking.

*"A vocabulary app that helps users memorize words"* cannot choose between showing 10 words per
session and 20. It is a description. `00_goal.md` instead carries a ranked list:

```
What matters, in order:
1. Zero setup — the user is learning within 10 seconds
2. Never lose user data
3. Works with no network
4. Speed
```

Now the question answers itself. Every acceptance criterion also carries a one-line constraint,
so a build session knows the *specific way* without asking and without being handed a tutorial:

```
G-004  User can open the app offline and still see saved words.
  proven by:      airplane mode → saved word list renders
  constrained by: reads from the local store; no network call on this path
```

Without that last line, "works offline" invites *"should I cache the response, use a local DB, or
sync on reconnect?"* — and that question costs you a round trip.

### 3. Nobody grades their own work

A build session can mark its work `built`. It **cannot** mark it `verified`.

Only a Phase 3.5 session can do that, and it starts fresh — it never sees the build session's
reasoning, its self-review, or its conformance table. It gets the goal, the contract, and the
code, and it is told to break them. It is forbidden to fix anything it finds; every finding goes
into a verdict file for a build session to act on.

The reason is simple: the reasoning that produces code is also the reasoning that justifies it.
An agent checking its own work writes tests that confirm what the code *does* rather than what the
contract *requires*. A different model is not needed — a session with no memory of the building is.

Because the agent now decides far more on its own, something has to check those decisions. This is
what pays for that.

### 4. Context survives as a contract, not a history

Each build session starts with no memory of the previous ones. What carries forward is a
**contract**.

A session building `billing.invoice-generator` never learns how `auth.session-store` was built.
It reads that sub-module's contract: exports and exact signatures, request/response shapes, error
names, env vars, guarantees, non-goals. Roughly 40 lines, where the implementation is 400 and is
pure noise to a consumer.

Three properties follow:

**Contracts are declared during planning, not discovered during building.** Otherwise a sub-module
built early cannot know about one built later, so it guesses.

**Per-session context is bounded by a sub-module's neighbor count, not by project size.** A
12-sub-module project and an 80-sub-module project cost about the same per session.

**Everything is plain files in the repo.** They survive session death, diff in git, and you can
read and correct them directly. No conversation memory, no vector store, no retrieval.

---

## When it stops

The system is built to stop rather than guess. Three files carry that, and only two ever reach you.

**`_incomplete.md`** — open questions. Phase 2.5 answers most of them from the goal. **Answers
carry no marker saying who wrote them**, so a later phase reads an answer rather than an author,
and you can edit any of them freely.

**`_drift.md`** — a frozen contract that cannot be honored. **This is the one thing that is never
automated.** A contract is a promise other sub-modules were built against, and those sub-modules
are forbidden to read the code that would reveal a silent change. You rule `APPROVED` or
`REJECTED`; Phase 2 amend mode applies it and marks affected sub-modules `stale`.

**Three failed verifications** of one sub-module stops the project. Three failures is a contract or
decomposition problem, not a coding problem, and a fourth attempt degrades the code further.

---

## Other guardrails worth knowing about

- **The goal outranks the contract.** A contract that cannot satisfy the criterion it claims is
  drift, not something to implement faithfully.
- **Never degrade to proceed.** A named list — a mock standing in for real behavior, a hardcoded
  value where config belongs, a swallowed `catch`, a `TODO` counted as done, a skipped test,
  config that must change before deploy. Blocked means file drift or ask. It never means
  substituting something easier and continuing.
- **Goal coverage is gated.** Phase 2 will not freeze contracts while a criterion has no
  sub-module building it, or a sub-module serves no criterion.
- **Size budgets are decomposition signals.** A contract over 150 lines means the sub-module is
  too big and gets split.
- **Lane discipline.** A build session writes only inside its `owns` paths and never reads another
  sub-module's source.
- **Production parity.** Development runs production-equivalent configuration. The invariants list
  exactly which values may differ at deploy — credentials and environment-specific values, nothing
  else.
- **Commit after every meaningful sub-task.** This is what makes a wrong decision cost one revert.

---

## Layout

```
AGENTS.md                     points any agent at MASTER_PROMPT.md first
_planning/
  MASTER_PROMPT.md            router + the laws + the budgets — read every session
  system/
    VERSION                   2
    STANDARDS.md              decision authority, prohibitions, code rules — every session
    00_understand.md          phase protocols, one loaded at a time
    01_modules.md
    02_plan.md
    02b_resolve.md
    03_build.md
    03b_verify.md
    04_integrate.md
    templates/                blank forms for every runtime artifact
  00_inputs/                  ── you drop raw material here. Any format.
  00_brief.md                 ── created by Phase 0. Non-technical. You check it.
  00_goal.md                  ── created by Phase 0. Criteria + ranked intent. Decision fuel.
  00_invariants.md            ── created by Phase 2, loaded by every build session
  01_module_map.md            ── the tree, the edges, goal coverage, the build order
  02_contracts/<id>.md        ── one frozen public surface per sub-module
  03_verdicts/<id>.md         ── verify results, read by the next build session on a failure
  _incomplete.md              ── ❓ questions
  _decisions.md               ── every decision made without asking, and what it derives from
  _drift.md                   ── contract change requests, ruled on by you
  _state.md                   ── per-sub-module status; drives routing
```

`system/` is the reusable part and is never edited during a build. Everything above it in the tree
is generated per project.

---

## Versions

`_planning/system/VERSION` says which version a copy is running.

**v2** — this one. Phase 0 front door, ranked intent, the two ask lists, Phase 2.5 Resolve,
Phase 3.5 Verify, goal coverage gating, smoke boot and acceptance proof at integrate.

**v1** — four phases, no goal file, the builder graded its own work. `git checkout v1`. A v1 copy
has no `VERSION` file, which is how you tell.

A project already running on v1 does not need to migrate. Each project holds its own copy of
`_planning/`, so updating this repo does not reach it.
