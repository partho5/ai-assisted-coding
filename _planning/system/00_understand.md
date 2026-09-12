# PHASE 0 — UNDERSTAND

**Goal:** turn a pile of raw material into two frozen files — a non-technical brief a product
person can check, and a goal file rich enough that later phases stop asking obvious questions.

**You write no code and no module map in this phase.**

**This phase is never automated.** Every other phase in this system is built to run without you.
This one is built to talk to you. The `STANDARDS.md` "never ask" list **does not apply here** —
it binds Phase 2 onward. Under-asking now is what causes the over-asking later.

---

## Step 1 — Inventory the inputs

Read everything in `_planning/00_inputs/`. Any format the user dropped there: `.md`, `.txt`,
`.docx`, `.csv`, `.pdf`, screenshots, exports, notes.

Also read `PROJECT.md` at the repo root if it exists, and whatever the user typed in their
invocation.

Produce an inventory before reading deeply — filename, what it appears to be, and whether you
could read it. **Say plainly what you could not read** rather than working around it silently. A
file you skipped is a gap in the brief, and the user may be able to convert or re-export it.

If `00_inputs/` is empty and the user gave no description, stop and ask for material. Do not
invent a project.

## Step 2 — Interview

This is the substance of the phase. Ask a lot, in **non-technical language**.

**Do not ask about:** stack, framework, language, database, ORM, hosting, schema, API shape,
error model, test framework, or any other engineering choice. Those belong to Phase 2, and asking
them here derails a conversation that should be about the product.

**Ask about, until nothing is ambiguous:**

- Who uses this, and what they do today instead.
- What a person can accomplish with it. In their words, not in nouns from a data model.
- What one real session looks like start to finish.
- What must never happen. What would make someone abandon it.
- What is explicitly out of scope, and why.
- **Where two reasonable choices exist, which one wins — and why.** These answers become the
  ranked intent, and they are the highest-value thing this phase produces.
- Scale and shape, only where it changes the product: how many users, how much data, offline or
  not, single user or shared.

Ask in batches. Group related questions, explain what each one changes, and give your own reading
so the user can correct rather than compose from scratch. Keep going over as many turns as it
takes — a fork left unsettled here becomes a stop in every build session that touches it.

## Step 3 — Write the brief

Create `_planning/00_brief.md` from `system/templates/00_brief.md`.

Plain language, no engineering terms. Budget 150 lines. Fill the input table honestly, including
anything you could not read.

Then **show it to the user and ask them to confirm it**. This is the file that answers *"is this
what I actually meant?"* — it has no value unless someone checked it.

Writing the brief will expose gaps the interview missed. Go back to Step 2 for those.

## Step 4 — Write the goal

Create `_planning/00_goal.md` from `system/templates/00_goal.md`.

**Core goal** — 3–5 lines. Every later session loads this, so every word costs.

**Acceptance criteria.** For each, apply the test: *what observable fact would be false if we
failed?* If you cannot write it as a fact that can be checked and can fail, it is not a criterion
— raise it as a `❓` instead of writing something vague.

Each criterion needs `constrained by:` — **one line** giving the specific way. Not a tutorial
(Law 7 still binds). Its job is to remove the ambiguity that would otherwise become a question in
a build session. *"Works offline"* invites *"cache the response, use a local DB, or sync on
reconnect?"*. *"reads from the local store; no network call on this path"* does not.

**Intent — the ranked list.** The part that actually replaces asking. Written as ranked
preference, never as description. Test each line: *does reading this settle a real fork?* If it
reads like a description of the product, it is wrong and belongs in the brief instead.

## Step 5 — Refuse to finish if incomplete

Do not close this phase unless all four hold:

- [ ] Every acceptance criterion is falsifiable and carries a `constrained by:` line
- [ ] The intent section has a **ranked** list, not a description
- [ ] At least **three** "when in doubt" rules, each settling a real fork
- [ ] The user has seen and confirmed `00_brief.md`

An intent section written as description is the exact failure that puts the over-asking back into
every later phase. It is worth another round of questions to avoid.

Anything still unsettled goes to `_planning/_incomplete.md` as a `❓` and is listed under "Open at
freeze" in the goal file.

## Step 6 — Close

Create `_planning/_state.md` from `system/templates/_state.md` with `phase: understood`.

Commit.

Report: the input inventory including anything unread, the criteria count, the ranked intent, and
anything still open. Tell the user the next invocation is the same command.

---

## Phase 0 exit checklist

- [ ] Every file in `00_inputs/` inventoried; unreadable ones named, not skipped silently
- [ ] Interview ran in non-technical language; no stack questions asked
- [ ] `00_brief.md` written, within 150 lines, confirmed by the user
- [ ] `00_goal.md` written, within 120 lines
- [ ] Every criterion falsifiable, every criterion has `constrained by:`
- [ ] Ranked intent list + at least three "when in doubt" rules
- [ ] Open items in `_incomplete.md`
- [ ] `_state.md` created, committed
