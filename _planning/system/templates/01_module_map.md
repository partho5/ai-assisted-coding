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

- **purpose:** <one line>
- **depends_on:** `<id>`, `<id>` — or `none`
- **consumed_by:** `<id>` — or `none`
- **contract:** `_planning/02_contracts/<id>.md`
- **owns:**
  - `<source path>`
  - `<test path>`

#### <module>.<sub-module>

- **purpose:**
- **depends_on:**
- **consumed_by:**
- **contract:**
- **owns:**

## Graph checks

- [ ] Acyclic
- [ ] `consumed_by` is the exact inverse of `depends_on` everywhere
- [ ] No sub-module has more than ~5 neighbors
- [ ] No sub-module is a layer (`utils`, `models`, `helpers`) rather than a capability
- [ ] `owns` paths disjoint
- [ ] Build order is a valid topological sort
