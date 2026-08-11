# OPEN QUESTIONS

status: awaiting answers
updated: <YYYY-MM-DD>

> **How to use this file:** every `❓` needs a non-empty `**Answer:**` line directly beneath it.
> Write your answer inline, right there. Leave the question text alone.
>
> Planning does not proceed until every question is answered. Partial answers are fine to write
> as you go — the next session checks for blanks and will tell you which are still open.
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

Questions raised after planning. Same rules — answer inline; the sub-module that raised it is
stopped until you do.

❓ **Q-<MODULE>-<n>** — <question>
_raised by: `<id>` in Phase <n>_
_blocks: implementation_

  **Answer:**
