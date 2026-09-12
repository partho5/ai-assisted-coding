# OPEN QUESTIONS

status: awaiting answers
updated: <YYYY-MM-DD>

> **How to use this file:** every `❓` needs a non-empty `**Answer:**` line directly beneath it.
> Write your answer inline, right there. Leave the question text alone.
>
> **Most of these are answered for you.** Phase 2.5 reads `00_goal.md` and answers every question
> the goal already settles, then stops once with only what genuinely needs you — presented with
> options weighed against the ranked intent and a recommendation. You are not expected to work
> through this whole file.
>
> **Answers carry no marker saying who wrote them.** An answer from Phase 2.5 looks exactly like
> one of yours, and you can edit any of them freely at any time. Later phases read an answer, not
> an author.
>
> `_blocks:` tells you what each question is holding up:
> `architecture` (the module split) → `contract` (a public surface) → `implementation` (code detail).
> Answer `architecture` questions first; they are the critical path.

---

## Global

Cross-cutting decisions that apply to the whole project.

❓ **Q-GLOBAL-1** — <question>
_blocks: architecture_
<optional: the viable options given this project's stated requirements, each with its
consequence. Never marked as recommended.>

  **Answer:**

❓ **Q-GLOBAL-2** — <question>
_blocks: contract_

  **Answer:**

---

## <module>

### <module>.<sub-module>

❓ **Q-<MODULE>-1** — <question>
_blocks: contract_

  **Answer:**

❓ **Q-<MODULE>-2** — <question>
_blocks: implementation_

  **Answer:**

---

## Appended during build

Questions raised after planning. Same rules — answer inline.

A build session that files one of these does **not** halt the project: the router sends it to
Phase 2.5 first. It reaches you only if Phase 2.5 escalated it, and `_state.md` lists it under
"Open blockers" when that happens.

❓ **Q-<MODULE>-<n>** — <question>
_raised by: `<id>` in Phase <n>_
_blocks: implementation_

  **Answer:**
