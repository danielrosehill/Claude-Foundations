# Claude Foundations — agent instructions

This repo is an **argued index**, not a link dump and not an implementation. It
documents how the components of my user-level Claude Code layer compose. Nothing
here is installed or executed; the components live in their own repositories.

## Files

| File | Role |
|---|---|
| `README.md` | The argument, the layer map, one block per component. Human entrypoint. |
| `components.json` | The same register, machine-readable. Authoritative on slot, role, dates, and adoption step. |
| `SCOPE.md` | In/out boundary and the resolved edge cases. Read before adding or removing anything. |
| `docs/adoption-order.md` | The dependency order between components, with the wrong turns recorded. |

`README.md` and `components.json` describe the same set and must agree. If they
diverge, `components.json` is authoritative on metadata (slot, role, visibility,
created, adoption step) and `README.md` is authoritative on prose.

## Rules

- **Do not add a component without checking `SCOPE.md`.** The most common wrong
  addition is a domain plugin or an agent workspace — those belong in
  `Claude-Code-Projects-Index`. The second most common is an unbuilt idea.
- **Assume succession, not coexistence.** New work on a slot extends or supersedes
  the existing work on it. Before adding an entry, identify which entry it succeeds
  and record it both ways (`supersedes` / `superseded_by`) plus what changed. A
  superseded entry is marked, never deleted — the reason an approach was not enough
  is the part that is expensive to rediscover. Exactly one `status: current` per
  lineage. Filing two components as parallel peers when one clearly followed the
  other is the characteristic failure here.
- **Verify before writing.** Repo names, visibility, and creation dates come from
  `gh repo view danielrosehill/<name> --json name,visibility,createdAt`, not from
  memory. Several of these repos have similar names.
- **Record absolute dates.** Never "recently". Bump the `Snapshot:` line in
  `README.md` and `snapshot` in `components.json` on any change to the component
  set.
- **No procedures, no personal context.** The filled-in versions of these patterns
  are private. This repo contains no hostnames, credentials, paths on my machines,
  or personal data.
- **Do not restate a component's README here.** One block per component: what slot
  it owns, and the one constraint that is not obvious from its own README. If a
  block grows past a few paragraphs, the material belongs in the component's own
  repo.

## Adding a component

Use `.claude/skills/add-component.md`.
