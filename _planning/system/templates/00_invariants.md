# INVARIANTS

> Loaded by every build session. Budget: 250 lines. Rules only — no rationale, no design essay.
> Every rule must trace to an answered `❓` or an explicit statement in the project description.
> Delete any section this project does not need. Never fill a section with a plausible default.

## Project

- **What it is:** <one paragraph>
- **Explicit non-goals:** <what this project will not do>

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
