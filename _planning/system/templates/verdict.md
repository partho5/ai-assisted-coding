# VERDICT — <sub-module id>

attempt: <n>
result: PASS | FAIL
date: <YYYY-MM-DD>

> Written by a Phase 3.5 session that did not build this sub-module.
> On FAIL, the next build session reads the Findings section and nothing else from this file.

## Contract conformance

| Contract item | Declared | Found in code | Verdict |
|---|---|---|---|
| | | | PASS / FAIL |

## Goal criteria

| Criterion | Observable fact true? | `constrained by:` honored? | Verdict |
|---|---|---|---|
| G-00n | | | PASS / FAIL |

## Degradation scan

| Check | Result |
|---|---|
| Mock / stub / placeholder outside a test file | clean / <where> |
| Hardcoded value where config is required | clean / <where> |
| Swallowed `catch` | clean / <where> |
| TODO / FIXME / "for now" | clean / <where> |
| Test asserts implementation instead of contract | clean / <where> |
| Skipped or disabled test | clean / <where> |
| Config that must change before deploy | clean / <where> |
| File-level feature comment present | clean / <where> |
| What-not-how comment on each function | clean / <where> |
| One function, one responsibility | clean / <where> |

## Tests

Run by the verifier, not reported from the build session.

```
<command>
<output as actually observed>
```

Do these tests test the contract, or the code? <answer>

## Findings

> FAIL only. Each one specific enough for a fresh build session to act on without this session.

| # | File:line | What is wrong | What the contract or criterion requires |
|---|---|---|---|
| 1 | | | |

## Not fixed here

This session changed no code, tests, or contract. Every finding above is for a build session.
