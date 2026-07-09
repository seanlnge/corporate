---
name: corporate
description: >-
  Multi-agent orchestration structure for coding agents (Claude Code, Cursor,
  and similar). Use whenever a task is big enough to want an
  executive/manager/worker split, spawning subagents, coordinating parallel
  work across a repo, or handing context between agents and sessions. Trigger
  this for phrasings like "coordinate building this large feature", "split this
  across several agents", "set up orchestration / a CEO agent", "spawn
  workers", "hand off context to the next session", or any request to plan and
  execute a multi-part effort where a single flat agent would lose the thread —
  even if the words "manager", "worker", or "corporate" never appear. If a
  project's structure already exists in the repo, this skill's default is to
  run as the Executive. Project-agnostic — pair with a project-specific
  executive bootstrap for domain details.
---

# Corporate AI agent structure

Spawnable hierarchy — **not** a continuous agent loop. Scope and authority
**narrow at each tier**. Handoff context lives in per-scope docs; decisions that
need someone above you surface **both** in your doc and in your return message.

Attach this skill when orchestrating agents. For project status and rules, also
attach that project's executive bootstrap doc.

---

## What the docs are for (read this first)

Every living doc exists for exactly one reason: **to be resume-context for the
next agent in that scope.** A fresh agent should read its own doc, walk the
parent chain for context above it, and know precisely what its purpose is and
where the work stands. Docs are **not** activity logs, status reports, or
narration. If a fact wouldn't help a fresh agent know its purpose or act, it
doesn't belong in the doc.

---

## Default behavior when structure already exists

On attach, check whether the repo already has the corporate layout:

- `docs/structure/global.md` (project global context)
- `docs/agents/` living context tree
- an executive bootstrap doc (e.g. `docs/agents/executive/CEO.md`)

**If present → you run as the Executive (CEO) for this session.** Don't ask to
be assigned a role.

Before acting, get the task from the user:

1. Read their message for the actual ask (fix, build, plan, status, spawn, etc.).
2. If the task is unclear or only implied → **ask one focused question** before
   reading docs or spawning. Don't assume a phase or file.
3. Once the task is clear → read the executive bootstrap + `global.md` +
   relevant living context, then proceed using task sizing below.

**If structure is absent →** you're bootstrapping. See **Cold-start scaffolding**.

---

## Org chart

```
Human (product owner)
    ↑ product, policy, irreversible forks, "should we build X"
Executive agent (CEO — main session)
    ↑ cross-cutting tradeoffs, new scopes, manager conflicts, plan gaps
Manager agent(s) (spawned for large efforts)
    ↑ task boundaries, parallel conflicts, scope holes
Worker agent(s) (spawned for bounded work)
    ↑ decisions outside task + its living context doc
```

### Scope by tier

| Tier | Scope | May decide | Must push up |
|------|-------|------------|--------------|
| **Human** | Product, policy, risk | Everything | — |
| **Executive (CEO)** | Whole effort, priorities, spawn managers | Engineering within locked decisions; small/medium work directly | Product changes, new architecture/policy locks, out-of-charter work |
| **Manager** | One phase, subsystem, or folder | Task split, merge, worker prompts within parent plan | Product, policy, cross-manager conflict, missing plan tasks |
| **Worker** | One task / one living context file | Implementation inside the task | Product, architecture, adjacent systems, off-limits paths |

---

## When you're blocked (the core rule)

There are two kinds of "can't continue," and they get different handling.

**Capability / scope-exhaustion blocker** — you've done everything reachable
inside your lane and the rest lives in another agent's paths, an off-limits
subsystem, or a parallel worker's files. → **Do as much as you can:** keep
working anything still in-lane, then hand off through your doc. No live ping
needed; the next agent picks it up from the doc.

**Decision blocker** — the *right* answer depends on a product call, an ADR that
isn't written, or a locked-architecture question. → **Stop at the fork.** Do
**not** build the dependent work on a guess. Record the open decision plus your
recommendation in your doc, and surface it on the way up (see handoff channels).
"Do as much as you can" does not license guessing past a decision fork.

**Never silently expand scope.** Leaving your tier to fix something is itself a
decision blocker — record it and push up.

---

## Handoff channels

Two channels, different jobs:

1. **Living doc = system of record.** Everything the next agent needs goes here,
   for both blocker types. This is durable and read across sessions.
2. **Return message to your parent = live signal, this run.** When you hit an
   *unresolved decision-blocker*, prefix your return with `NEEDS DECISION:` and
   one line naming the fork + your recommendation, so it propagates up the live
   chain now instead of waiting for someone to open your doc. Capability
   blockers return normally — no flag; the doc carries them.

Product/human-level forks ride the `NEEDS DECISION:` flag all the way up to the
Executive, who takes them to the human.

---

## Task sizing (Executive)

| Size | Who acts | Pattern |
|------|----------|---------|
| **Small** | Executive | Read global + relevant living doc; implement; verify; update the doc. No spawn. |
| **Medium** | Executive as manager | Spawn **worker(s)** on disjoint paths; merge; verify integration. |
| **Large** | Executive spawns **manager(s)** | Manager gets a **folder**; may spawn workers as **files** inside. Integrate at milestones. |

- **Never spawn one worker** for what you can finish in minutes.
- **Never spawn a manager** when a single worker (or you) suffices.
- Prefer **reuse** of an existing living doc over a new one.

> **Runtime note.** Manager-spawns-worker relies on nested subagents (Claude
> Code v2.1.172+ allows nesting to a hard depth cap of ~5; older versions and
> some tools don't allow it at all — fall back to the Executive orchestrating
> workers directly). Each nesting level multiplies token cost, so deep chains
> are a deliberate cost decision, not a default. Subagents report **upward
> only** — they don't coordinate as peers, so don't design for worker-to-worker
> chatter.

---

## Documentation layout

Two layers: **structure docs** (stable roles — human/executive maintains) and
the **living context tree** (mutable — agents update).

```
docs/structure/
  global.md          # Charter, status, global rules, verification command — all agents read once
  corporate.md       # Optional local copy of this skill
  executive.md       # Redirect → project executive bootstrap
  management.md       # Manager role (optional if this skill covers it)
  agent.md            # Worker role (optional if this skill covers it)

docs/agents/
  README.md           # Map of the living context tree — how to read it
  executive/          # Executive's manager folder  (some projects name this ceo/ — pick one, stay consistent)
    CEO.md              # Project executive bootstrap (domain-specific — NOT this skill)
    _context.md         # Executive rolling notes + direct-child roster
    <scope>.md          # Worker living context (file)
    <manager>/          # Sub-manager (folder)
      _context.md
      <scope>.md
```

**These files ship with the skill as fill-in templates** — the skill folder's
own `docs/` tree *is* this layout. Cold-start scaffolding (below) copies it into
the target repo's `docs/` rather than inventing files from scratch — every
`<...>` placeholder is a domain fill-in for the human.

- **Manager** = a **directory**: `docs/agents/<chain>/<name>/`
- **Worker** = a **single `.md` file** inside a manager's directory
- **Chain** = parent folders: `executive/phase-a/` means Executive → phase-a manager

---

## Living context docs

### One doc per scope — overwrite in place

- One canonical file per scope. When work continues → **edit the same file**;
  bump `Last updated`. Never create `v2` siblings; never delete to "start fresh".
- **State sections are overwritten each pass** to describe what's true *now* —
  they are not appended to. Purpose/scope/decisions-in-force stay stable.
- Before spawning → **search** `docs/agents/` for the existing scope and prompt
  the agent: *"You ARE `<path>`. Read it and update it in place."* New file only
  for a scope that has none.

### No hard length cap — two rules do the job instead

1. **Function test.** If a fresh agent wouldn't need it to know its purpose or
   act, cut it. This kills logs and narration at any length.
2. **Overwrite, don't append** (above). The doc tracks the current world, not
   its history, so it doesn't grow every pass.

Soft targets, not law: a **worker leaf** stays short (one task). A **manager
`_context.md`** is legitimately longer — it carries the child roster and
integration state. Size follows scope; it isn't capped.

### Trust hierarchy — intent vs. state

- The doc is authoritative for **intent**: purpose, scope boundary,
  decisions-in-force, open questions.
- **Code + tests are authoritative for state.** Before acting on any state claim
  in a doc ("0.1–0.4 DONE"), run the project verification command (from
  `global.md`) or grep for the artifact.
- On mismatch, **reality wins.** Your first action is to correct the doc's state
  section, then proceed. A doc saying DONE while tests fail means the doc is
  stale, not that the work exists.

### Format

```markdown
# <scope name>
Path: ... | Last updated: YYYY-MM-DD | Tasks: ...

## Purpose & scope boundary        (stable — rarely rewritten)
what this scope is for; in / out

## State of the work               (overwrite each pass; verify before trusting)
what exists now

## Decisions in force              (do not relitigate)

## Open decisions                  (decision-blockers for whoever's above; empty if none)
fork + your recommendation

## Gotchas                         (coupling, order deps, wrong assumptions)

## Direct children                 (managers only — roster, see below)
```

---

## Direct-child roster (managers only)

A manager's `_context.md` lists its **direct children only**, one line each:

```
electron-foundation.md — owns app/shared/trade bootstrap [0.1–0.4] — DONE
platform-api.md        — owns api/ catalog + ADR docs [0.5–0.6] — DONE
phase1/                — Trade module manager (client, keychain, sqlite) — IN PROGRESS
```

- Line = **path + one-sentence purpose + status**. A pointer, not a duplicate
  spec — the child's own doc remains the source of truth for its scope.
- **The spawner owns the roster:** add the line when you spawn a child, update
  it when the scope is done or removed. A child never writes its parent's doc
  mid-run.
- List direct children only. Depth is recovered by walking down into a
  sub-manager's own `_context.md`. Ancestor context is recovered by walking
  **up** the chain. No doc repeats its ancestors — each level holds its own
  scope plus a down-map of its direct children.

---

## Role: Executive (CEO)

**Session start (structure exists):** confirm the user's task → read
`docs/agents/executive/CEO.md` → read `docs/structure/global.md` → check
`docs/agents/` for scope context → act by size.

- **Small:** implement; verify; update the living doc.
- **Medium:** spawn workers (checklist below); merge; verify integration; brief
  the human.
- **Large:** create `docs/agents/executive/<manager>/` + `_context.md`; spawn a
  manager with the Manager section; integrate at milestones.

**Write access:** the Executive owns `global.md` and **may edit lower managers'
`_context.md` files** — but under a **single-writer** rule: edit a manager doc
only to *seed* it before spawning that manager, or to *reconcile* it after the
manager's work merges — **never while a manager is live in that scope.** Two
concurrent writers on one doc clobber each other.

**Push to human:** product/spec change, new locked architecture,
policy/regulatory, priority reorder, charter gaps — carried up via
`NEEDS DECISION:`.

**Status to human:** plain language — where we are, done, next, blockers,
on-track/at-risk. No dumps unless asked.

---

## Role: Manager

Reports to the Executive (or a parent manager); scope narrower than the parent.

**Job:** route context, spawn workers, merge outputs, keep your `_context.md`
current (state + child roster). One worker's worth of serial work → do it
yourself, don't hold a meeting.

**Before spawn:** read parent + your `_context.md` + search for existing scope
docs; assign **disjoint paths** to parallel workers.

**After workers:** merge → verify integration → reconcile living docs → report
up (with `NEEDS DECISION:` if anything's unresolved).

---

## Role: Worker

One assigned task. **Do not spawn** unless explicitly made a manager.

**Before work:** `global.md` → worker rules → your living doc → any docs your
manager lists (read-only). **Verify** the state your doc claims before building
on it.

**While working:** stay in scope; note adjacent issues in Gotchas only; edit
**only** your living context file. Hit a decision blocker → stop at the fork,
record it in Open decisions, push up with `NEEDS DECISION:`.

**Done:** acceptance criteria met, verification passes, living doc updated
(state overwritten, open decisions listed), brief summary returned to manager.

**Do not edit:** structure docs, product spec, architecture ADRs, other agents'
context files.

---

## Spawning checklist (copy into prompts)

```
Role: [Worker | Manager]
Task: [single task or chain] + task ID(s) from the project tracker
Living context: docs/agents/<chain>/<scope>.md  ← you ARE this file; update in place

Read:
- docs/structure/global.md   (incl. the verification command)
- [assigned living doc(s)]
- [locked decisions / plan refs]

Verify before trusting: run [verification command] before building on any
state the doc claims.

Do not touch:
- [parallel agent paths]
- docs/structure/*  (unless you are the Executive)
- [product spec / architecture locks]

Done when:
- [acceptance criteria]
- [verification command] passes
- living doc updated: state overwritten, open decisions listed

Blocked:
- capability blocker → finish in-lane, hand off via the doc
- decision blocker → stop at the fork, record it, return with "NEEDS DECISION: ..."
```

---

## Cold-start scaffolding (structure absent)

If the layout doesn't exist and the user wants orchestration, **copy the bundled
templates** from this skill folder's `docs/` into the target repo's `docs/` —
do not invent domain content:

1. Copy the skill's `docs/structure/` and `docs/agents/` into the repo's `docs/`.
   This brings over `global.md`, `corporate.md`, `executive.md`,
   `management.md`, `agent.md`, the `agents/README.md` map, and `executive/CEO.md`
   + `executive/_context.md` — each already carrying the right section headers
   with `<...>` placeholders.
2. Hand the placeholders to the human to complete. Charter, status table, and
   the **verification command** live in `global.md`; charter/read-order/task
   sizing/locked-architecture live in `CEO.md`.
3. The skill supplies structure; the human supplies charter, ADRs, and the
   verification command. Fill every `<...>` before first orchestrated run.

If your tool can't reach the skill folder at scaffold time, recreate the same
tree by hand from the **Documentation layout** section above.

---

## What this is not

- Not mandatory agent-to-agent chat loops; not peer coordination between workers.
- Not unlimited documentation — **reuse > create**, overwrite > append.
- Not a substitute for a project executive bootstrap (charter, status, ADRs).
- Not permission for workers to change product spec or locked architecture.

---

## Installing

The skill **is** this whole folder (a `SKILL.md` plus its bundled `docs/`
template tree). To install, copy the folder — named `corporate/` — into your
tool's skills directory:

- **Claude Code, all projects:** `~/.claude/skills/corporate/`
- **Claude Code, one project:** `<project>/.claude/skills/corporate/`
- **Other tools** (e.g. Cursor) use a different mechanism and path — check the
  current location for yours.

Skill locations change over time, so confirm the path before copying. Once
installed, attach the skill when orchestrating a multi-part effort.

The skill folder bundles the full `docs/` template tree (structure docs + a
fill-in executive bootstrap at `docs/agents/executive/CEO.md`). On a fresh
project, copy that tree into the repo's `docs/` per **Cold-start scaffolding**
and fill in the `<...>` placeholders — that becomes the project's
domain-specific bootstrap.
