# Scope

**Last reviewed: 2026-08-12**

This index covers repositories that reshape the **user-level layer** of a Claude
Code install — the context, directives, procedures, and capability that are in
place before any task is known. It is an argued index, not a complete one: the
value is in stating how the components compose, so a repo that does not compose
with the others does not belong even if it is topically adjacent.

## In scope

A repo belongs here if all three hold:

1. **It changes the layer, not the task.** It governs what Claude loads, knows, or
   can reach before being asked — rather than performing some domain workflow.
2. **It owns a slot.** It maps onto one of the five slots in the README's layer
   map (entrypoint, reference context, standing directives, procedures,
   capability), or it is tooling that performs/audits one of those slots.
3. **It exists and has been used.** Not an idea, not a placeholder, not a repo
   whose README describes something unbuilt.

Maintenance tooling counts. So does a pattern with no code, if the pattern is what
is being adopted.

## Out of scope

- **Domain plugins and agent workspaces** — purchasing, transcription, sysadmin,
  research. They *consume* this layer; they are not part of it.
  → [Claude Code Projects Index](https://github.com/danielrosehill/Claude-Code-Projects-Index)
- **Third-party context-engineering tools** — this index is exclusively my own
  work. → [Context Engineering Resources](https://github.com/danielrosehill/Context-Engineering-Resources)
- **Project-level-only `CLAUDE.md` work** — per-repo templates, dev-versus-user
  splits, batch CLAUDE.md seeders, repo-type template sets. Adjacent, and mostly
  already listed in the projects index under *Context and Personalization*.
- **Filled-in personal context.** The private, populated versions of these
  patterns, and the private planning repo where friction and roadmap live.
- **Harness feature requests and bug reports.** Complaints about the tool are not
  changes to my layer.

## Edge cases, and how they were resolved

| Case | Verdict | Reason |
|---|---|---|
| [Claude Rudder](https://github.com/danielrosehill/Claude-Rudder) — repo-level chunking, plus MCP and feedback tooling | **In**, as maintenance tooling | Its `chunk-claude` half is the same pattern one level down, and the two layers must agree on their boundary. Listed for that half only; the MCP/feedback primitives are out of scope and not described here. |
| [Claude Vault](https://github.com/danielrosehill/Claude-Vault) — dormant plugin activation | **In**, as slot 5 | Capability is loaded before the task is known, same as context is. Excluding it would make the index a context index rather than a foundations index. |
| [Claude Context Analysis 0526](https://github.com/danielrosehill/Claude-Context-Analysis-0526), [State Of Claude Context 0426](https://github.com/danielrosehill/State-Of-Claude-Context-0426) | **Listed as evidence, not components** | They measure the problem rather than changing the layer. Kept because the measurement is the justification, and an index that only shows conclusions cannot be argued with. |
| [Document As You Go](https://github.com/danielrosehill/Document-As-You-Go) — superseded by Habits Of Claude | **In**, under slot 3 | Superseded in mechanism, not retired: it is still the clearest single-habit demonstration, and the lineage explains why Habits Of Claude is shaped as it is. |
| [ClaudeMD Turnstile](https://github.com/danielrosehill/ClaudeMD-Turnstile) — dev vs user `CLAUDE.md` | **Out** | Project-level concern about audience, not about the always-on user layer. |
| [The User Voice Types](https://github.com/danielrosehill/The-User-Voice-Types) — snippets for tolerating dictation errors | **Out** | Genuine standing directives, but the delivery mechanism is loose snippets. If they are ever folded into `habits/`, they arrive here through Habits Of Claude rather than as their own entry. |
| [Claude Style Switcher](https://github.com/danielrosehill/Claude-Style-Switcher) — tone and output-shape fragments | **Out** | Same reason: drop-in fragments for a prompt, not a slot in the layer. |

## Deliberate overlap

Every component here is also listed in
[Claude Code Projects Index](https://github.com/danielrosehill/Claude-Code-Projects-Index),
mostly under *Context and Personalization*. That is intended and should not be
resolved. The projects index answers *what exists*, alphabetically, across ~100
repos. This one answers *what fits together, in what order, and why* — which is
not expressible as a section in a flat list, and which is the whole point of a
separate repo.

The rule when they disagree: the projects index is authoritative on a repo's
existence, description, and badges. This index is authoritative on which slot it
occupies and how it composes.
