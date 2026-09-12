# MODULE MAP

status: draft | frozen
updated: <YYYY-MM-DD>

> Sub-module ids are `<module>.<sub-module>`, kebab-case, stable for the life of the project.
> `consumed_by` must be the exact inverse of all `depends_on`. The graph must be acyclic.
> `owns` paths must not overlap between sub-modules.

## Build order

Topological, leaves first. Ties broken by fewest dependents.

1. `<id>`
2. `<id>`

## Modules

### <module>

<one line: what this module is responsible for>

#### <module>.<sub-module>

- **purpose:** <one line, in the language of `00_brief.md` — not in stack terms>
- **satisfies:** `G-00n`, `G-00n`
- **depends_on:** `<id>`, `<id>` — or `none`
- **consumed_by:** `<id>` — or `none`
- **contract:** `_planning/02_contracts/<id>.md`
- **owns:**
  - `<source path>`
  - `<test path>`

#### <module>.<sub-module>

- **purpose:**
- **satisfies:**
- **depends_on:**
- **consumed_by:**
- **contract:**
- **owns:**

## Goal coverage

Every criterion in `00_goal.md`, and who owns it. **Phase 2 cannot freeze contracts while this
table has a hole in either direction.**

| Criterion | Satisfied by | |
|---|---|---|
| `G-001` | `<id>`, `<id>` | ok |
| `G-002` | — | **orphan criterion — nothing builds this** |

| Sub-module satisfying nothing | |
|---|---|
| `<id>` | **orphan sub-module — serves no stated goal** |

An orphan criterion means the plan provably cannot deliver what the project is for. An orphan
sub-module is either scope creep or a goal the brief forgot to state. Both are decisions, not
formatting: raise a `❓` rather than deleting a row to make the table clean.

## Graph checks

- [ ] Acyclic
- [ ] `consumed_by` is the exact inverse of `depends_on` everywhere
- [ ] No sub-module has more than ~5 neighbors
- [ ] No sub-module is a layer (`utils`, `models`, `helpers`) rather than a capability
- [ ] `owns` paths disjoint
- [ ] Build order is a valid topological sort
- [ ] Every criterion has an owner; every sub-module has a criterion
