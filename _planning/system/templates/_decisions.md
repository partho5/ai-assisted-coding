# DECISIONS

> Every decision made without asking the user, and what it derives from.
> Append-only. Never rewrite or delete an entry — supersede it with a new one.
>
> A decision recorded here is auditable. **A decision not recorded here is an assumption.**

Read this before deciding something similar, so the project stays consistent with itself.

| id | decision | derives from | phase | date |
|---|---|---|---|---|
| D-001 | <what was decided> | G-004 `constrained by:` / intent rank 1 / "when in doubt" rule 2 / never-ask list | 3 | YYYY-MM-DD |

**`derives from` must name a real source.** One of:

- a `G-` criterion, its `proven by:` or its `constrained by:` line
- a numbered rank in the intent list
- a specific "when in doubt" rule
- an item on the never-ask list in `STANDARDS.md` §1
- a prior `D-` entry this one follows from

If you cannot name one of those, you did not derive the decision — you guessed. Raise a `❓`
instead of writing the entry.

## Superseded

| id | superseded by | why |
|---|---|---|
