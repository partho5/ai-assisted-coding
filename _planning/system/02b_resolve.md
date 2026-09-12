# PHASE 2.5 — RESOLVE

**Goal:** answer every open `❓` that the goal already settles, and stop **once** with only what
genuinely needs the user.

This phase exists because v1 stopped the project on every open question. Most of those questions
had an answer sitting in the project's own goal — nobody had written the goal down in a form that
could answer them. Now they can be answered.

**You write no code in this phase.**

---

## Step 1 — Load

Read exactly:

1. `_planning/system/STANDARDS.md`
2. `_planning/00_goal.md` — all of it, not just the core goal
3. `_planning/00_brief.md`
4. `_planning/_incomplete.md`
5. `_planning/_decisions.md`, if it exists — so you stay consistent with earlier decisions

**Do not read** any source, contract, or the module map. You are settling decisions, not
designing. If a question cannot be answered without reading a contract, it is a contract question
and belongs to Phase 2.

## Step 2 — Sort every question

For each unanswered `❓`, decide which of three it is. Use `STANDARDS.md` §1.

**Answerable** — the core goal, a `constrained by:` line, the ranked intent, or a "when in doubt"
rule settles it. Also anything on the **never-ask list**, which should not have been asked at all.

**Must escalate** — it is on the **always-ask list**: which LLM, which database, auth model, a
persisted schema, anything costing money per use, anything a user sees and judges, a permanent
external dependency, anything expensive or impossible to change later.

**Genuinely undecidable** — not on the always-ask list, but the goal does not reach it either.
Apply the two-part test. If it is cheap to reverse, **answer it** and note the thin derivation.
If it is expensive to reverse, escalate.

When two answers both fit, the ranked intent decides. That is what the ranking is for — use it
rather than treating the tie as a reason to escalate.

## Step 3 — Answer the answerable

Write the answer **inline in `_incomplete.md`**, in the same form a user's answer takes.

**Leave no marker saying who answered.** No "resolved by agent", no timestamp that only your
answers carry, no distinguishing format. A later phase must not be able to tell your answer from
the user's, and the user must be able to edit any answer without it looking different afterward.

For each answer, append one line to `_planning/_decisions.md`:

```
D-014  <the decision>  ← G-004 constrained by / intent rank 1 / "when in doubt" rule 2
```

Name what it derives from. A derivation you cannot name means you guessed — escalate instead.

## Step 4 — Stop once, with the rest

Leave the escalated questions unanswered and stop.

Present them to the user **in one batch**, and for each one:

- what it is, in plain language
- the options
- **how each option scores against the ranked intent**
- **your recommendation, and why**

Never a neutral menu. The user's job is to confirm or overrule a reasoned position, not to work
the decision out from scratch. A question you present without a recommendation is work you
handed back.

If nothing needs escalating, say so and route on rather than stopping.

## Step 5 — Close

Update `_state.md` — leave `phase` unchanged; this phase does not advance it. Record under "Open
blockers" which question ids still need the user.

Commit.

Report: how many questions were open, how many you answered, how many need the user, and the
batch itself.

---

## Resolve exit checklist

- [ ] Every `❓` sorted: answered, or escalated for a named reason
- [ ] Every answer written inline, with **no marker identifying the answerer**
- [ ] Every answer has a `_decisions.md` line naming what it derives from
- [ ] No answer written without a derivation you can name
- [ ] Escalated questions presented in one batch, each with options scored against the ranked
      intent and a recommendation
- [ ] `_state.md` updated, committed

---

## The failure to avoid

Answering a question the ranked intent does not actually settle, and writing a derivation that
sounds plausible but is not real. That is guessing with a citation attached, and because your
answers are indistinguishable from the user's, nobody will catch it.

When the derivation is thin and the decision is expensive to reverse, escalate. Escalating one
question costs one line in a batch the user is reading anyway.
