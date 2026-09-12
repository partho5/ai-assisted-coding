# STANDARDS

> Loaded every session, every phase. Budget: 60 lines. If a rule serves one phase, it belongs in
> that phase file, not here.

## 1. Decision authority — binds Phase 2 onward

Phase 0 and Phase 1 ignore this block. Planning is meant to ask.

**Never ask. Decide, and log one line in `_planning/_decisions.md`:**
naming · file and folder layout · internal signatures not fixed by a contract · a standard library
for a solved problem · log wording · test file naming · code style and formatting · whether to
extract a helper · field ordering · internal timeouts and retry counts · error text no user sees.

**Always ask. Never decide alone:**
which LLM or model · which database · auth model · a persisted data schema · anything that costs
money per use · anything a user sees and judges · an external dependency that becomes permanent ·
anything expensive or impossible to change later.

**Anything else — ask only if BOTH are true:**
1. it is expensive or impossible to change later, **and**
2. it cannot be derived from the core goal, a `constrained by:` line, or the ranked intent in
   `_planning/00_goal.md`.

Otherwise decide, and record it. **An unrecorded decision is an assumption.**

When you must ask, present the options already weighed against the ranked intent, with a
recommendation. Never a neutral menu.

## 2. Never degrade to proceed

These are drift, not shortcuts. Each one makes a failure invisible until assembly.

- a mock, stub, fake or placeholder standing in for real behavior outside a test file
- a hardcoded value where config or an environment variable is required
- a `catch` that swallows without handling
- `TODO`, `FIXME`, "for now" or "temporary" counted toward done
- a test asserting what the code currently does instead of what the contract requires
- a skipped or disabled test
- narrowing scope without filing drift
- configuration that must be changed before deploy

Blocked? File drift, or raise a `❓`. **Never substitute something easier and continue.**

## 3. Code rules

- One comment at the top of each file naming the feature it serves.
- One or two lines above each function saying **what** it does. Never how.
  Purpose: an inspecting agent gets context without reading bodies. This is a token-cost control.
- One function, one responsibility. Reusable. No near-duplicate variants.
- Core features plug in and out. Couple only when the feature cannot exist alone.
- Development config equals production config. Deployment changes credentials and
  environment-specific values only — nothing else.
- Never leave a change that breaks what already worked. Run the existing tests before committing.

## 4. Commit

Git commit after every meaningful (sub)task. This is what makes a wrong decision cost one revert
instead of a debugging campaign.
