# CONTRACT — <module>.<sub-module>

frozen: <YYYY-MM-DD>
budget: 150 lines — over budget means the sub-module is too big, not that the file needs trimming

> **Surface only.** Everything here is something a consumer can observe. Nothing here explains
> how to build it. No implementation steps, no algorithms, no internal helpers, no example
> bodies. Delete sections that do not apply — never fill one with a plausible default.

## Purpose

<one or two lines>

## Depends on

`<id>`, `<id>` — or `none`

## Exports

```
<exact signatures with full types>
```

For each, one line on what it does — observable behavior, not mechanism.

## Routes

| Method | Path | Request | Response | Status codes |
|---|---|---|---|---|

Request and response schemas, field by field, with types, required/optional, and constraints:

```
<schemas>
```

## Events

- **Emits:** `<name>` — `<payload shape>` — when
- **Consumes:** `<name>` — `<payload shape>` — effect

## Persistent state

| Object | Access | Notes |
|---|---|---|
| `<table / collection / key>` | read / write / both | |

## Errors

| Name / code | Condition | Surfaced as |
|---|---|---|

Consumers may rely on these exact identities. Raising an undeclared error is a contract breach.

## Configuration

| Env var | Required | Purpose |
|---|---|---|

## Side effects

- External calls:
- Filesystem:
- Queues / timers / background work:
- `none` if none.

## Guarantees

What a consumer may rely on — ordering, idempotency, atomicity, nullability, concurrency
behavior, retry semantics. State only what is actually guaranteed.

## Non-goals

What this sub-module explicitly does **not** do, so no consumer assumes it does.
