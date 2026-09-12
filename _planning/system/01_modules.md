# PHASE 1 — MODULES

**Goal:** turn the brief and the goal into (a) a draft module tree, with every sub-module tied to
the goal criteria it serves, and (b) the scope questions the decomposition raises.

**You write no code in this phase. None.**

**Asking posture: moderate.** Phase 0 settled what the product is. This phase asks only about
**scope and boundaries** — what belongs together, what is one capability versus two, what is in
this version. Product questions belong to Phase 0; technical questions belong to Phase 2. If you
find yourself wanting to ask what the product should do, Phase 0 did not finish and you should
say so rather than patching it here.

---

## Inputs

- `_planning/system/STANDARDS.md`
- `_planning/00_brief.md`
- `_planning/00_goal.md` — all of it
- Nothing else. Do not read the codebase; at this stage there generally isn't one, and if there
  is, the brief and goal govern.

## Outputs

1. `_planning/01_module_map.md` — **draft** (dependencies may be incomplete; that's expected)
2. `_planning/_incomplete.md` — scope questions, if any
3. `_planning/_state.md` — `phase: modules` with the draft sub-module list

---

## Step 1 — Decompose

Break the app into **modules**, then modules into **sub-modules**.

Sizing rule for a sub-module: *one focused session can build it and its tests to completion
without running out of room to think.* Concretely, aim for something whose public surface fits in
a 150-line contract and whose implementation is roughly one to a few source files.

Split by **capability boundary**, not by file type. `auth.session-store` and
`auth.password-reset` are sub-modules. `models`, `utils`, and `helpers` are not — they are layers,
and splitting that way guarantees every sub-module depends on every other one.

Test each split against the fan-out rule: if a sub-module would need to know the contracts of more
than ~5 neighbors, the boundary is wrong. Re-cut it.

Describe each sub-module in the **language of the brief**, not of a stack. *"Keeps a user signed
in between visits"*, not *"JWT middleware"*. The technical shape is Phase 2's job, and naming it
here freezes a decision nobody has made.

For each sub-module record: **id**, one-line purpose, provisional `depends_on`, and `satisfies`.

**Existing code your new work depends on** is a normal `depends_on` entry with status `adopted`.
Find those by **reading the code**, not by guessing from filenames — a dependency inferred from a
name is how a feature gets built against a function that does not do what it sounds like.

**Existing files your new work must modify** — register a route, add a migration, extend a model —
go in `extends` on the sub-module that needs them, one line per touch-point:

```
extends:
  - src/routes/index.ts — register the new route
```

`owns` is exclusive: you made it, you may rewrite it. `extends` lets you **add at the named point
and nothing else**. Two sub-modules may `extends` one file; neither may `own` it.

## Step 2 — Tie every sub-module to the goal

This is what stops a project from building 22 correct things that add up to the wrong product.

**Every sub-module lists `satisfies: [G-00n, ...]`** — the acceptance criteria it helps make true.

Then build the coverage table at the bottom of the map: every `G-` id, and which sub-modules own
it. Two failures to look for:

- **An orphan criterion** — a `G-` id no sub-module satisfies. The plan cannot reach the goal.
  Either a sub-module is missing, or the criterion is out of scope and belongs in Non-goals. Raise
  it as a `❓`.
- **An orphan sub-module** — satisfies no criterion. Either it serves something the goal forgot to
  state, or it is scope creep. Raise it as a `❓`.

Both are checkable by reading the table, which is the point. Phase 2 will refuse to freeze
contracts while either exists.

## Step 3 — Ask what the decomposition raises

Only scope and boundary questions. For example: is this one capability or two; is this in the
first version; does this sub-module own the data or borrow it; which module owns a behavior that
two could plausibly claim.

### The bar for asking

`STANDARDS.md` §1 binds from here on. Before writing any `❓`:

1. Is it on the **never-ask list**? Then decide it and log a `_decisions.md` line.
2. Does the core goal, a `constrained by:` line, the ranked intent, or a "when in doubt" rule
   settle it? Then it is settled — write the answer inline as the answer, and log the derivation.
3. Is it on the **always-ask list**? Then it is a `❓`, always.
4. Otherwise ask only if it is **expensive or impossible to change later**.

Do not ask the same thing twice in different words — one question, under the sub-module that owns
it, referenced from elsewhere if shared.

Questions you do raise are **collected, not blocking**. Phase 2.5 will answer what it can and
batch the rest.

## Step 4 — Write `_incomplete.md`

Use `system/templates/_incomplete.md`. Rules:

- Mirror the project structure: `## Global`, then `## <module>`, then `### <module>.<sub-module>`.
- Each question is a line starting with `❓`, followed by an indented `**Answer:**` line left blank.
- Stable id: `Q-<module>-<n>` or `Q-GLOBAL-<n>`. Downstream files reference these.
- Where there is a small closed set of viable options **given this project's goal**, list them
  with their consequence. Never mark one as recommended — Phase 2.5 does the recommending, after
  weighing them against the ranked intent.
- Tag what each question blocks: `_blocks: contract | architecture | implementation_`.
- Order within a section: `architecture` first, then `contract`, then `implementation`.

Write nothing else into this file. It is a form, not a document.

## Step 5 — Write the draft map and state

`01_module_map.md` from the template, marked `status: draft`, with `satisfies` on every
sub-module and the coverage table filled.

`_state.md` from the template, `phase: modules`, every sub-module `pending`, `attempts: 0`.

## Step 6 — Close

Commit.

Report to the user:
- module / sub-module count
- goal coverage: any orphan criterion, any orphan sub-module
- question count, and which block contract freezing

Then route on. Open questions go to Phase 2.5, not to the user — do **not** stop and ask them to
answer inline. Stop only if goal coverage has a hole, because that means the plan cannot reach
the goal and no later phase can fix it.

---

## Phase 1 exit checklist

- [ ] Every sub-module has an id, a purpose in the brief's language, `depends_on`, and `satisfies`
- [ ] No sub-module is a layer (`utils`, `models`, `types`) rather than a capability
- [ ] No sub-module has more than ~5 neighbors
- [ ] Coverage table written; orphan criteria and orphan sub-modules named
- [ ] No stack, schema, or API decision made or implied
- [ ] Never-ask items decided and logged in `_decisions.md`, not asked
- [ ] Every `❓` sits under the sub-module that owns it
- [ ] Zero lines of code written
- [ ] `_state.md` updated, committed
