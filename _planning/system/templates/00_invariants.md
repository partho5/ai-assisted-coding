# INVARIANTS

> Loaded by every build session. Budget: 250 lines. Rules only — no rationale, no design essay.
> Every rule must trace to an answered `❓`, a line in `00_goal.md`, or an explicit statement in
> `00_brief.md`. Delete any section this project does not need. Never fill one with a plausible
> default.
>
> **`system/STANDARDS.md` binds on top of this file and is loaded in the same sessions.**
> Do not restate anything from it — decision authority, the no-degradation list, comment rules,
> single responsibility, commit cadence. Repeating them wastes the budget every build session
> pays forever. This file holds what is specific to **this project's stack**.
>
> **`00_goal.md` holds what the project is for.** This file holds how it is built.

## Technical scope

- **Runtime shape:** <what kind of thing this is — service, app, CLI, job>
- **Explicit technical non-goals:** <what this codebase will not support>

## Stack

- Language / version:
- Framework / version:
- Package manager:
- Runtime target:
- Database / driver:

## Layout

```
<directory tree with a one-line rule for what belongs in each>
```

- Where a new sub-module's source goes:
- Where its tests go:
- Where shared types live:

## Naming

- Files:
- Functions / methods:
- Types / classes:
- Database tables / columns:
- Routes:
- Environment variables:

## Error model

- How an error is represented:
- How errors are raised:
- How errors propagate across a boundary:
- What an external caller sees on failure:
- What must never appear in an error surfaced externally:

## Logging

- Logger and how it is obtained:
- Required log points:
- Levels and when each is used:
- Never logged:

## Configuration & secrets

- How config is read:
- How secrets are supplied:
- Rule: no inline literal for anything configurable.

## Production parity

Development runs production-equivalent configuration. No prototype setup that must be swapped
before deploy.

- Same in dev and prod: <services, drivers, protocols, auth mechanism>
- **May differ at deploy — the complete list:**
  | Value | Why it must differ |
  |---|---|
  | <credential / endpoint / env-specific value> | |

Anything not on that list must not need changing to deploy. If something does, the configuration
is wrong and gets fixed here — not written down as a deployment step. Phase 4 checks this against
the running system.

## Security

- Authentication model:
- Authorization model — who may do what, and where the check happens:
- Input validation — required at every public surface; rules:
- Output encoding:
- Query/command construction rules (parameterization required, no string building):
- Never logged, never returned, never committed:

## Data layer

- Access pattern:
- Migrations — tool, location, naming:
- Transaction rules:
- Rules for concurrent access:

## Testing

- Framework:
- Test file location and naming:
- A test must cover: happy path, every declared error, input validation, declared guarantees.
- Dependencies are stubbed strictly to their declared contracts.
- Definition of passing:

## Dependencies

Permitted, beyond the stack above:

- <name — purpose>

Adding anything not on this list requires a `❓`. Never add a dependency silently.

## Commands

Run exactly as written:

- Install:
- Lint:
- Format:
- Test:
- Build:
- Run:

## Prohibitions

- <hard rules — things that must never appear in this codebase>
