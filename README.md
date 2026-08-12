# Claude Foundations

**Snapshot: 2026-08-12**

The foundational layer of my Claude Code setup — the part that is in force *before*
any task is known.

Most of what I publish for Claude Code is a tool for doing a thing: a plugin for
purchasing, a skill for transcription, an MCP server for a printer. This index
covers a different category. These are the repositories that reshape the
**user-level layer itself** — what Claude loads on every session, how it behaves
before being asked, and where it goes looking when it needs more. They are not
alternatives to each other. They are components of one system, and each one owns
a different slot in it.

---

## Contents

- [The one move](#the-one-move)
- [Layer map](#layer-map) — which repo governs which path on disk
- [Components](#components)
  - [Slot 1 — The entrypoint](#slot-1--the-entrypoint)
  - [Slot 2 — Reference context](#slot-2--reference-context)
  - [Slot 3 — Standing directives](#slot-3--standing-directives)
  - [Slot 4 — Procedures](#slot-4--procedures)
  - [Slot 5 — Capability](#slot-5--capability)
  - [Maintenance tooling](#maintenance-tooling)
- [Adoption order](#adoption-order)
- [Evidence](#evidence)
- [What is deliberately not here](#what-is-deliberately-not-here)
- [Related indexes](#related-indexes)

---

## The one move

Every component here is the same move applied to a different slot:

> Replace an eagerly-loaded bulk with a **lean always-on pointer** plus
> **on-demand retrieval**.

The user-level context window is a budget spent on every turn of every session,
in every directory. A monolithic `~/.claude/CLAUDE.md` charges you for the LAN map
while you are writing CSS. A fully-installed plugin marketplace charges you for
157 plugin descriptions to use one. In both cases the fix has the same shape: keep
the routing table resident, push the payload behind a lookup, and make the agent
fetch it when the task actually calls for it.

Stated that way it sounds like housekeeping. It is not. It changes what you can
afford to tell Claude at all. Once reference material is free until used, the
useful size of your context store stops being bounded by your context window —
and the always-on file can be spent entirely on **directives**, which are the
only thing that has to be resident to work.

That last distinction is the one that took longest to learn and is the easiest to
get wrong:

| | Lives in the always-on file | Lives behind a pointer |
|---|---|---|
| **A rule** — obeyed | ✅ | ❌ a directive moved out of the always-on file stops being followed |
| **A lookup** — consulted | ❌ | ✅ |

Split on that line, not on file size.

---

## Layer map

Which component governs which path, and whether it costs context on every turn.

| Path | Loaded | Governed by |
|---|---|---|
| `~/.claude/CLAUDE.md` — overall shape | Every session, every directory | [Split CLAUDE.md Pattern](#slot-1--the-entrypoint) |
| `~/.claude/CLAUDE.md` — directive block | Every session, every directory | [Habits Of Claude](#slot-3--standing-directives) |
| `~/.claude/context/*.md` | On demand, per topic | [Claude User Context Pattern](#slot-2--reference-context) |
| Private SOP library (path in plugin config) | On demand, per procedure | [Claude SOPs](#slot-4--procedures) |
| Installed plugins / skills | On demand, via substrate lookup | [Claude Vault](#slot-5--capability) |
| Repo-level `CLAUDE.md` + `context/` | Per repo | [Claude Rudder](#maintenance-tooling) |

The two rows marked *every session, every directory* are the entire budget under
discussion. Everything else in this table used to be in those rows.

---

## Components

### Slot 1 — The entrypoint

The shape of the file that is always loaded: a lean routing table, not a
knowledge base.

#### Split CLAUDE.md Pattern
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Split-Claude-MD-Pattern)
![Template](https://img.shields.io/badge/Template-Ready-green?style=flat-square)

The originating pattern (2026-03-20). Establishes that `~/.claude/CLAUDE.md` is a
compact entrypoint of ~30–50 lines — identity, autonomy, key paths, and a pointer
to `context/` for everything else — and documents *why*: Claude Code's loading is
**additive across directory levels**, so the user-level file is charged on top of
every project-level file, in every session, forever.

Also settles a trap worth knowing before you start: both `~/.claude/CLAUDE.md`
and `~/CLAUDE.md` are picked up. Maintaining both gives you two overlapping
always-on contexts and defeats the exercise. Pick one.

---

### Slot 2 — Reference context

Where the payload goes once it leaves the entrypoint.

#### Claude User Context Pattern
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Claude-User-Context-Pattern)
![Template](https://img.shields.io/badge/Template-Ready-green?style=flat-square)

A depersonalised, forkable version of my actual `~/.claude/` layer (2026-05-13):
a lean top-level file routing to topical files under `context/` —
`system-environment.md`, `git-rules.md`, `mcp-usage.md`, `file-organization.md`,
`troubleshooting.md`, and so on — each with `<placeholders>` where the personal
specifics were.

Two things here are load-bearing beyond the folder layout. The `<files>` index in
the top-level file can mark individual files `priority="critical"`, which is how
you tell Claude to consult something eagerly without paying to inline it. And the
whole directory is portable: cloning it onto a new machine and swapping the
placeholders gives a fresh install an assistant that already knows the lay of the
land.

---

### Slot 3 — Standing directives

The one thing that must be resident to work — so it gets the always-on file, and
it gets treated as a first-class unit rather than as prose.

#### Habits Of Claude
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Habits-Of-Claude)

One file per **habit** — a standing disposition in force before the task is known
— assembled by script into a pasteable markdown block and a JSON index, then
spliced into a marker-delimited region of the target prompt. The region in
`~/CLAUDE.md` is a **build target**: you edit `habits/`, run the assembler, and
never hand-edit the output.

The insight was never any one instruction. It was that the useful unit is the
habit. A single continuous system prompt obscures that, and gets edited by
accretion until nobody can say what it currently asks for; one file per
disposition can be added, revised, reconciled against drift, and installed
selectively into any CLAUDE.md.

#### Document As You Go
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Document-As-You-Go)

The ancestor (2026-07-25): a drop-in system prompt and slash command making
agents capture what they discover — undocumented APIs, real auth flows, dead ends
— instead of losing it when the session ends. Habits Of Claude is this one
generalised, and it remains the best single-habit demonstration of the idea.

---

### Slot 4 — Procedures

Multi-step processes I re-run: too long to keep resident, too specific to invent
each time.

#### Claude SOPs
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Claude-SOPs)

Your own standard operating procedures as a private, versioned markdown library
Claude reads on demand: one `CLAUDE.md` pointer, one index, one procedure loaded
per run.

The split is the point and is easy to get backwards. The **public** repo holds
only machinery — the `sop-*` skills, `scripts/sop.py`, the format spec. The
**procedures** live in a separate private library whose path sits in the plugin's
config. Machinery is shareable; procedures are personal and often sensitive.

---

### Slot 5 — Capability

The same argument, applied to tools rather than to text. What Claude *can do* is
also loaded before the task is known, and is also mostly irrelevant on any given
turn.

#### Claude Vault
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Claude-Vault)
· [design notes](https://github.com/danielrosehill/Claude-Vault-Idea)

A meta-plugin holding a vault of dormant plugins and MCP servers, activated
per-project. Targets the eager plugin-description loading problem: install a
marketplace at user level and you pay for every plugin's description in every
session, whether or not the project has anything to do with it.

The generalisation of this — reaching a large skill library through a search-and-
read substrate rather than installing it — is what the entrypoint's pointer table
is for. Cost comparison, in my own setup: listing 157 plugins eagerly runs to
roughly 122k tokens per session; two substrate lookup tools cost about 1.1k.

---

### Maintenance tooling

The patterns above describe a target state. These perform and audit it.

#### User CLAUDE.md Plugin
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/User-Claude-MD-Plugin)

Operates on the user-level layer directly: `chunk-user-claude` (prune the
entrypoint and push each section to the right file under `~/.claude/context/`),
`user-claude-health` (audit entrypoint size, pointer-table drift, context-file
inventory, duplication, MEMORY.md size), `list-user-context`, and
`edit-user-context` (targeted edit that keeps the pointer table in sync).

The health audit is the part that matters over time. The split is easy to perform
once and easy to let rot — pointer tables drift out of sync with the files they
point at, and a stale pointer is worse than no pointer.

#### Claude Rudder
[![View Repo](https://img.shields.io/badge/View%20Repo-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Claude-Rudder)

The same operations one level down, at repo level: `chunk-claude` for a project's
`CLAUDE.md` and `context/` store, plus `context-health-check` and
`should-i-start-fresh`. Included here because the repo-level split is the same
pattern, and because the two layers have to agree on the boundary between them —
project knowledge in the repo's `context/`, machine and workflow knowledge at
user level.

---

## Adoption order

The components are independent enough to adopt piecemeal, but not in any order —
see [`docs/adoption-order.md`](docs/adoption-order.md). Short version: split the
entrypoint before writing anything new into it, stand up the context store before
the habits block (or the habits get buried in reference material), and leave the
capability layer for last because it is the only one that is not reversible in a
single edit.

---

## Evidence

These were not designed on a whiteboard. They came out of measuring where the
context budget actually went:

| Repo | What it measured |
|---|---|
| [Claude Context Analysis 0526](https://github.com/danielrosehill/Claude-Context-Analysis-0526) | Redacted point-in-time dump of `/context` from a heavily-pluginned session — where the budget goes, what is eager versus lazy |
| [State Of Claude Context 0426](https://github.com/danielrosehill/State-Of-Claude-Context-0426) | Q&A notes on where bloat accrues given the harness's current shape and primitives |

---

## What is deliberately not here

- **Domain plugins and workspaces.** Anything that does a task rather than
  shaping the layer the task runs in. Those are indexed at
  [Claude Code Projects Index](https://github.com/danielrosehill/Claude-Code-Projects-Index).
- **My actual context.** The patterns are depersonalised on purpose; the filled-in
  versions are private. Nothing here contains credentials, hostnames, or personal
  data.
- **Project-level `CLAUDE.md` templates.** One-off per-repo templates, developer
  versus user split, batch seeders. Adjacent, but not the user-level layer.
- **Ideas not yet built.** Friction and roadmap live in a separate private
  planning repo, not here. A component earns an entry when it exists and has been
  used.

See [`SCOPE.md`](SCOPE.md) for the full in/out boundary.

---

## Related indexes

| Index | Territory |
|---|---|
| [Claude Code Projects Index](https://github.com/danielrosehill/Claude-Code-Projects-Index) | Everything — workspaces, plugins, templates. This repo is the argued subset. |
| [Claude Code Plugins](https://github.com/danielrosehill/Claude-Code-Plugins) | The plugin marketplace itself |
| [Context Engineering Resources](https://github.com/danielrosehill/Context-Engineering-Resources) | Other people's context-engineering tooling |
