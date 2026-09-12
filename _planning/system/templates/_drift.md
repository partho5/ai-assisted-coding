# CONTRACT DRIFT LOG

> A build or integration session files an entry here when a frozen contract cannot be honored,
> or when reality contradicts the plan. The session then **stops**.
>
> **You rule on drift, not the agent.** Set `status` to `APPROVED` or `REJECTED` and add your
> ruling. Phase 2 amend mode then applies it and marks affected sub-modules `stale`.
>
> This is the one thing v2 did not automate. Open questions are answered by Phase 2.5 from the
> goal; contract changes are not. A frozen contract is a promise other sub-modules were built
> against, and those sub-modules are forbidden to read the code that would reveal a silent change.
> Drift is the only channel between sub-modules that never see each other.
>
> Statuses: `OPEN` (awaiting your ruling) → `APPROVED` / `REJECTED` → `RESOLVED` (amend applied).

---

## D-001 — <short title>

- **status:** OPEN
- **raised by:** `<id>` in Phase <n>, <YYYY-MM-DD>
- **contract:** `_planning/02_contracts/<id>.md`
- **threatens:** `G-00n` — or `none`. Which acceptance criterion this puts at risk. If the
  contract cannot satisfy a criterion it claims, the **goal wins** and the contract changes.

**Declared**

<what the contract says, quoted exactly>

**Reality**

<what is actually required, and the concrete reason the declaration cannot stand — an error,
a platform limit, a conflict with another contract or with the invariants. Not a preference.>

**Proposed change**

<the minimum edit to the contract that resolves it>

**Blast radius**

- Consumers affected: `<id>`, `<id>` — or `none`
- Providers affected: `<id>` — or `none`
- Invariants affected: <section, or none>
- Sub-modules that would become `stale`: `<id>`, `<id>`

**Alternatives considered**

<other ways to resolve it without changing the contract, and why they do not work>

**Your ruling:**

<!-- Set status above, then write your decision here. -->

---

## D-002 — <short title>

- **status:**
- **raised by:**
- **contract:**

**Declared**

**Reality**

**Proposed change**

**Blast radius**

**Alternatives considered**

**Your ruling:**
