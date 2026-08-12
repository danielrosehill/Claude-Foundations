---
name: add-component
description: Add a repository to the Claude Foundations index, or record why it was rejected. Use when a new user-level layer repo has been built, or when asked whether something belongs in this index.
---

# Add a component

Work through these in order. Do not skip the scope check — the index's value is
that membership means something.

## 1. Dedupe

Read `components.json`. If the repo is already listed under any name, stop and
update the existing entry instead. Check `SCOPE.md`'s edge-case table too: it may
have already been considered and rejected, in which case say so and stop unless
the reason has since changed (e.g. the repo has been restructured).

## 2. Scope check

Apply the three tests in `SCOPE.md`:

1. Does it change the layer rather than perform a task?
2. Does it own one of the five slots, or maintain one?
3. Does it exist and has it been used?

If any test fails, do not add it. Instead add a row to the edge-case table in
`SCOPE.md` with the verdict and the reason, and — if it is a domain plugin or
workspace — point at `Claude-Code-Projects-Index` as the right home.

If the repo owns a slot that is not in the current list of five, that is a real
finding: say so explicitly rather than forcing it into the nearest existing slot,
and propose the new slot before writing anything.

## 3. Gather facts, do not recall them

```bash
gh repo view danielrosehill/<NAME> --json name,description,visibility,createdAt,url \
  -q '[.name,.visibility,(.createdAt|.[0:10]),.url,.description]|@tsv'
gh api repos/danielrosehill/<NAME>/readme --jq .content | base64 -d
```

Several repos in this space have near-identical names (`Claude-User-Context-Pattern`
vs `Split-Claude-MD-Pattern` vs `User-Claude-MD-Plugin` vs `Claude-MD-Experiments`).
Confirm you have the right one from its README, not its name.

## 4. Write the entry

**`components.json`** — add to `components` (or `evidence`, if it measures the
problem rather than changing the layer), with: `name`, `url`, `slot`, `role`
(`pattern` | `system` | `tooling` | `predecessor`), `visibility`, `created`,
`adoption_step`, `summary`, `key_constraint`. Add `scope_note` if only part of the
repo is in scope, and `superseded_by` if applicable.

**`README.md`** — one `####` block in the matching slot section: name, a
`View Repo` badge, then prose. The prose must carry:

- what slot it owns and what it replaces;
- **the one thing that is not obvious from the repo's own README** — the trap, the
  ordering constraint, the thing that is easy to get backwards. This is the only
  reason for the block to exist. If you cannot name it, the entry is not ready.

Do not restate the component's feature list. Do not exceed three short paragraphs.

## 5. Place it in the adoption order

Decide where the component sits in `docs/adoption-order.md`. If it introduces a
new dependency — "X must be in place before this works" — add it to the step and
to the summary table's *Blocked by* and *Fails as* columns. Say what it looks like
when adopted out of order; a step with no failure mode is probably not a real step.

## 6. Bump and ship

- `Snapshot:` line in `README.md` and `snapshot` in `components.json` → today's
  absolute date.
- `Last reviewed:` in `SCOPE.md` if the scope text changed.
- Verify `README.md` and `components.json` list the same set.
- Commit describing which component was added and which slot it took, then push.
