# GOAL

> Written once, in Phase 0. Frozen afterward — changing it is a `_drift.md` entry.
> Budget: 120 lines. This is decision fuel, not a description. `00_brief.md` is the description.
> Every line here exists to settle a fork that would otherwise become a question.

## Core goal

<3–5 lines. Loaded by every session afterward, including every build session. What this product
is for, in the plainest language that still distinguishes it from a neighboring product.>

## Acceptance criteria

Each one is a fact that can be **checked** and can **fail**. The generating test:
*what observable fact would be false if we failed?*

`proven by:` is how you check it — an executable check where possible, an observation otherwise.
`constrained by:` is the one line that tells a build session the **specific way**, without
becoming a tutorial. It exists to kill the "how should I do this?" question before it is asked.
**Every criterion carries one.**

| | |
|---|---|
| **G-001** | <statement — an observable fact> |
| proven by | <the check that would fail> |
| constrained by | <the one line that removes the ambiguity> |
| status | unproven \| proven |

<repeat>

A criterion you cannot write as a checkable fact is not yet a criterion. Raise it as a `❓`
instead of writing a vague one.

## Non-goals

- <what this project will explicitly not do>

## Intent — what matters, in order

> This section is what replaces asking. Write it as **ranked preference**, never as description.
> A description cannot choose between two options. A ranking can.
> *"Helps users memorize words"* cannot decide between 10 words per session and 20.
> *"Zero setup, ranked first"* can.

### What matters, in order

Lower rank loses when two collide.

1. <the thing that wins over everything else>
2. <...>
3. <...>

### When in doubt

Minimum three rules. Each must be written so that reading it settles a real fork.

- Two options both work → <which one, and why it follows from the ranking>
- <fork> → <winner>
- <fork> → <winner>

## Open at freeze

<Anything Phase 0 could not settle, carried as `❓` in `_incomplete.md`. Empty is the goal.>
