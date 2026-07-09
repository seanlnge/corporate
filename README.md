# corporate_structure_skill

A multi-agent orchestration structure for coding agents (Claude Code, Cursor,
and similar). It gives a big, messy effort a shape — an executive that plans and
spawns, managers that own subsystems, workers that own bounded tasks — and, more
importantly, a discipline for **handing context between agents and across
sessions** so a fresh agent can pick up any scope and know exactly what it's for.

It is a spawnable hierarchy, not a continuous agent loop. Scope narrows at each
tier; unresolved decisions surface upward.

## The idea in one paragraph

A single flat agent loses the thread on large work: its context fills with file
reads and dead ends, and nothing survives the session. This skill splits the
work across tiers and keeps the durable state in small per-scope docs. The docs
exist for exactly one reason — **to be resume-context for the next agent in that
scope.** Not logs, not status reports. A fresh agent reads its own doc, walks the
parent chain for the context above it, and knows its purpose and where the work
stands.

## Org chart

```
Human (product owner)     — product, policy, irreversible forks
Executive (CEO)           — whole effort, priorities, spawns managers
Manager(s)                — one phase / subsystem / folder
Worker(s)                 — one task / one living-context file
```

Authority narrows going down. Anything outside your tier's scope is pushed up,
never silently absorbed.

## How blocking and handoff work

There are two kinds of "can't continue," handled differently:

- **Capability blocker** — you've finished everything in your lane and the rest
  lives elsewhere (another agent's paths, an off-limits subsystem). Do as much as
  you can in-lane, then hand off through your doc. No live ping needed.
- **Decision blocker** — the right answer needs a product call, an unwritten ADR,
  or a locked-architecture answer. **Stop at the fork.** Don't build on a guess.
  Record the open decision plus your recommendation in the doc, and fire a
  `NEEDS DECISION:` line in your return so it propagates up the live chain now
  instead of waiting to be read next session.

The **living doc is the system of record** for both. The `NEEDS DECISION:`
return is the live nudge for the class of problem that can't wait — product
forks ride it all the way up to the human.

## The living docs

- **One doc per scope**, edited in place. State sections are **overwritten each
  pass** to describe what's true now; purpose and locked decisions stay stable.
  No `v2` copies, no delete-to-restart.
- **No hard length cap.** Two rules keep docs lean instead: a *function test*
  (if a fresh agent wouldn't need it to know its purpose or to act, cut it) and
  *overwrite-don't-append*. A worker leaf stays short; a manager doc is
  legitimately longer because it holds a roster of its children.
- **A manager lists its direct children only**, one line each —
  `path — one-sentence purpose — status`. The spawner owns that roster; the
  child's own doc stays the source of truth for its scope. Ancestor context is
  recovered by walking up the chain, so no doc repeats its parents.
- **Code and tests are authoritative for state; the doc is authoritative for
  intent.** Before building on any state a doc claims, run the project
  verification command. On mismatch, reality wins — fix the doc first.

## Layout

```
docs/structure/
  global.md        # charter, status, global rules, verification command
  corporate.md     # optional local copy of this skill
  ...

docs/agents/
  executive/       # (some projects name this ceo/)
    CEO.md           # project executive bootstrap — domain-specific, NOT this skill
    _context.md      # executive notes + direct-child roster
    <scope>.md       # a worker's living context (file)
    <manager>/       # a sub-manager (folder)
      _context.md
      <scope>.md
```

A **manager** is a directory; a **worker** is a single `.md` file inside one.

## Install

Copy this folder into your tool's skills directory and attach it when you're
orchestrating a multi-part effort. Locations differ by tool and change over time,
so check the current path for yours — Claude Code uses `.claude/skills/`; Cursor
uses a different mechanism.

The skill is project-agnostic. Pair it with a project-specific executive
bootstrap at `docs/agents/executive/CEO.md` (charter, read order, task sizing,
locked architecture, verification command). If the structure doesn't exist yet,
the skill will scaffold a skeleton and hand you the domain fill-ins.

## Runtime notes

- Manager-spawns-worker relies on nested subagents. Claude Code v2.1.172+ allows
  nesting to a hard depth cap of about 5; older versions and some tools don't
  allow it at all, in which case the executive orchestrates workers directly.
  Each nesting level multiplies token cost, so deep chains are a deliberate
  choice, not a default.
- Subagents report **upward only** — they don't coordinate as peers. Don't
  design for worker-to-worker chatter; route everything through the parent.

## What this is not

- Not mandatory agent-to-agent chat loops, and not peer coordination.
- Not unlimited documentation — reuse over create, overwrite over append.
- Not a substitute for a project executive bootstrap.
- Not permission for workers to change product spec or locked architecture.
