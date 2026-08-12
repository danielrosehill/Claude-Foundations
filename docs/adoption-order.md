# Adoption order

**Written 2026-08-12.** Order derived from the sequence I actually went through
between 2026-03-20 and 2026-08-12, with the wrong turns marked.

The five slots are independent enough to adopt piecemeal, but there are real
dependencies between them. Adopting out of order does not fail loudly — it
produces a layer that looks correct and behaves worse than the monolith you
started with. That is the failure mode to watch for.

## 0. Measure first

Before moving anything, read `/context` in a session opened the way you normally
open one — in a project directory, with your usual plugins installed, not in a
clean home directory. The number that matters is not the size of your `CLAUDE.md`;
it is the share of the resident budget going to things irrelevant to the session
in front of you.

Two things reliably surprise people here, and both are documented in
[Claude Context Analysis 0526](https://github.com/danielrosehill/Claude-Context-Analysis-0526):
plugin and MCP tool descriptions are often a larger line item than the prompt
itself, and user-level loading is *additive* on top of project-level loading
rather than replacing it.

Skipping this step is why the capability slot (5) gets adopted last by people who
measure and never at all by people who do not.

## 1. Split the entrypoint

→ **fork** [Claude User Context Pattern](https://github.com/danielrosehill/Claude-User-Context-Pattern)
· **read** [Split CLAUDE.md Pattern](https://github.com/danielrosehill/Split-Claude-MD-Pattern) for the reasoning

Two repos cover this step because the second is the successor to the first. The
Split pattern states the problem and the loading model; the User Context Pattern is
the same split worked through to a directory you can clone. Take the reasoning from
one and the files from the other.

Do this before writing anything new into `CLAUDE.md`, because the split changes
where new material *should* go, and material written into the monolith in the
meantime has to be re-sorted by hand.

The sort criterion is not size and not topic. It is:

- **A rule — something obeyed.** Stays resident. A directive moved behind a
  pointer stops being followed; this is the single most expensive mistake in the
  whole exercise, and it is silent.
- **A lookup — something consulted.** Moves out.

Before starting, resolve the two-locations trap: `~/.claude/CLAUDE.md` and
`~/CLAUDE.md` are both loaded. Keeping both gives you two overlapping always-on
contexts. Pick one and delete the other.

**Wrong turn I took:** an early pass sorted by section length, which moved a
short, critical git-push policy out to a context file and left a long, rarely
relevant hardware inventory resident. The policy stopped being applied within a
day, and it was not obvious why — nothing errors, the model just no longer knows
the rule.

## 2. Stand up the context store

→ [Claude User Context Pattern](https://github.com/danielrosehill/Claude-User-Context-Pattern)

The destination for everything step 1 evicted. Do it as part of the same session
as step 1 — an entrypoint that has been pruned but whose pointer table points
nowhere is strictly worse than the monolith, because the material is now
unreachable rather than merely expensive.

Two details worth getting right immediately rather than retrofitting:

- Give each pointer a **"read it when" trigger**, not just a filename. A table of
  filenames is a list of things the agent might read; a table of triggers is a
  routing decision it can actually make.
- Use `priority="critical"` on the handful of files that should be consulted
  eagerly. This is the escape hatch for material that sits awkwardly between rule
  and lookup.

## 3. Install the maintenance tooling

→ [User CLAUDE.md Plugin](https://github.com/danielrosehill/User-Claude-MD-Plugin)

Install before the layer needs maintaining, not after. The specific decay to
guard against is **pointer drift**: files get renamed, merged, or deleted while
the pointer table keeps advertising them. A stale pointer is worse than a missing
one — the agent believes the material exists and either hunts for it or improvises
around its absence.

`user-claude-health` is the reason this step exists. Run it after any restructure.

## 4. Convert directives into habits

→ [Habits Of Claude](https://github.com/danielrosehill/Habits-Of-Claude)
· lineage: [Document As You Go](https://github.com/danielrosehill/Document-As-You-Go)

Only now, with the entrypoint pruned, is it worth treating the resident directive
block as a first-class artefact. Doing this before steps 1–2 buries the habits in
reference material, where they read as background rather than as instructions and
get diluted accordingly.

From this point the `~/CLAUDE.md` habits region is a **build target**: edit
`habits/`, run the assembler, splice. Hand-edits inside the markers get
overwritten on the next install, and — worse — drift silently until
`reconcile-habits` catches the divergence between what the repo says and what the
prompt actually contains.

Keep environment facts out of `habits/`. Which MCP server fronts what, which
hostnames exist, which package manager to use — those are lookups and belong in
the context store from step 2. Habits are dispositions, not configuration.

## 5. Externalise procedures

→ [Claude SOPs](https://github.com/danielrosehill/Claude-SOPs)

Anything multi-step that you re-run: too long to keep resident, too specific to
re-derive. Comes after the context store because the two are easy to confuse, and
the boundary only becomes clear once the store exists.

The distinction that holds: a context file describes **how things are**; an SOP
describes **what to do, in order, to get an outcome**. If it has steps and an
outcome, it is an SOP.

Keep the procedures in a private library and the machinery public. The default is
the wrong way round for most people's instincts — the procedures feel like the
shareable artefact, and they are the part that encodes real account names, real
paths, and real business process.

## 6. Then, and only then, the capability layer

→ retrieval over a library rather than installation of one.
Predecessor, for the reasoning: [Claude Vault](https://github.com/danielrosehill/Claude-Vault)

Last, for two reasons. It is the only slot whose adoption is not reversible by a
single edit — uninstalling a marketplace and re-reaching its contents through a
substrate touches the whole install. And its payoff is only legible once steps 1–5
have shrunk the text side of the budget; while the prompt is still the dominant
line item, the tool-description saving looks like noise.

**Do not stop at the intermediate step.** The obvious first move is to keep the
library installed and manage which parts are awake per project — that is what
Claude Vault does, and it is a real improvement over installing everything hot. It
is also where it is tempting to stop, and stopping there keeps two costs: something
must still describe the dormant set for it to be selectable, and the per-project
activation state is manual work that goes stale exactly the way a pointer table
does. Go to retrieval: nothing is described until it is searched for, and there is
no per-project state at all.

In my own setup this step is the largest single saving of the six — roughly 122k
tokens per session of eager plugin listing against about 1.1k for the two lookup
tools that replace it, over a library of about 1,800 skills, none installed
(measured 2026-08-12).

## Summary

| # | Step | Blocked by | Fails as |
|---|---|---|---|
| 0 | Measure `/context` in a realistic session | — | Adopting slot 5 never, or on faith |
| 1 | Split the entrypoint | 0 | Rules moved behind pointers stop being obeyed |
| 2 | Stand up the context store | 1 (same session) | Pointer table pointing nowhere |
| 3 | Install maintenance tooling | 2 | Pointer drift; stale pointers |
| 4 | Convert directives into habits | 1, 2 | Directives diluted by reference material |
| 5 | Externalise procedures | 2 | SOPs and context files conflated |
| 6 | Capability layer | 0, and ideally 1–5 | Saving looks like noise; hard to reverse; or stalling at per-project activation |
