# PHASE 1 — INTAKE

**Goal:** turn a project description into (a) a draft module tree and (b) an exhaustive
`_incomplete.md` containing every open decision, nested under the module it belongs to.

**You write no code in this phase. None.**

---

## Inputs

- The project description the user gave you, or `PROJECT.md` if it exists.
- Nothing else. Do not read the codebase — at intake there generally isn't one, and if there
  is, the user's description governs.

## Outputs

1. `_planning/01_module_map.md` — **draft** (dependencies may be incomplete; that's expected)
2. `_planning/_incomplete.md` — every open question
3. `_planning/_state.md` — `phase: intake` with the draft sub-module list

---

## Step 1 — Decompose

Break the app into **modules**, then modules into **sub-modules**.

Sizing rule for a sub-module: *one focused session can build it and its tests to completion
without running out of room to think.* Concretely, aim for something whose public surface fits
in a 150-line contract and whose implementation is roughly one to a few source files.

Split by **capability boundary**, not by file type. `auth.session-store` and
`auth.password-reset` are sub-modules. `models`, `utils`, and `helpers` are not — they are
layers, and splitting that way guarantees every sub-module depends on every other one.

Test each split against the fan-out rule: if a sub-module would need to know the contracts of
more than ~5 neighbors, the boundary is wrong. Re-cut it.

For each sub-module record: **id**, one-line purpose, provisional `depends_on`.

## Step 2 — Interrogate

Walk the description and find every place where a decision is *not determined by what the user
actually said*. Each becomes a `❓`.

Question these categories at minimum, and only skip one when the description genuinely settles it:

**Global / cross-cutting**
- Language, framework, versions, package manager, runtime target
- Persistence: what database, ORM/driver, migration strategy
- Authentication and authorization model; session vs token; who can do what
- Secrets and configuration handling
- Error representation and propagation; what the user/client sees on failure
- Logging, and whether observability is in scope
- Test framework, and what level of coverage counts as done
- Deployment target, if it constrains architecture
- Hard non-goals: what this project explicitly will not do

**Per module / sub-module**
- Exact data shapes and field-level rules (required, nullable, format, bounds)
- Validation rules and what happens when validation fails
- Every error condition and its handling
- Boundary behavior: empty, missing, duplicate, concurrent, oversized
- External services, and behavior when they are unavailable
- Anything with a policy component: retention, limits, rate, ordering, permissions

### The bar for asking

Ask when a competent engineer could reasonably pick two different answers and the choice is
visible in the finished product.

Do **not** ask about things fully determined by an answer you already have, and do not ask the
same thing twice in different words — one question, placed under the sub-module that owns it,
referenced from elsewhere if shared.

### The bar for *not* deciding

You may **not** resolve a question by:
- what is conventional or most common
- what a similar project usually does
- what seems safest or most flexible
- what you would choose

Any of those is a Law 2 violation. Write the `❓`.

The single exception: a choice with **no externally visible consequence and no downstream
dependency** — pure internal implementation detail, invisible in the contract. Those are the
build session's business and are not questions.

## Step 3 — Write `_incomplete.md`

Use `system/templates/_incomplete.md`. Rules:

- Mirror the project structure: `## Global`, then `## <module>`, then `### <module>.<sub-module>`.
- Each question is a line starting with `❓`, followed by an indented `**Answer:**` line left blank.
- Give each question a stable id: `Q-<module>-<n>` or `Q-GLOBAL-<n>`. Downstream files reference these.
- Where the answer is genuinely open-ended, say so. Where there is a small closed set of viable
  options **given this project's stated requirements**, list them with their consequence — that
  is help, not assumption. Never mark one as recommended.
- Tag what each question blocks: `_blocks: contract | architecture | implementation_`.
- Order within a section: `architecture` first, then `contract`, then `implementation`.

Write nothing else into this file. It is a form, not a document.

## Step 4 — Write the draft map and state

`01_module_map.md` from the template, marked `status: draft`.
`_state.md` from the template, `phase: intake`, every sub-module `pending`.

## Step 5 — Stop

Report to the user:
- module/sub-module count
- question count, broken down by section
- which questions block contract freezing (those are the critical path)

Then stop and ask them to answer inline in `_planning/_incomplete.md`.

Do not proceed to Phase 2 in the same session. Phase 2 begins in a fresh session after the
user has answered.

---

## Phase 1 exit checklist

- [ ] Every sub-module has an id, a purpose, and provisional `depends_on`
- [ ] No sub-module is a layer (`utils`, `models`, `types`) rather than a capability
- [ ] No sub-module has more than ~5 neighbors
- [ ] Every `❓` sits under the sub-module that owns it
- [ ] No question is answered, hinted at, or pre-decided
- [ ] Zero lines of code written
- [ ] `_state.md` updated
