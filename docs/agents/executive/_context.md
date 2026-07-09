# Executive — rolling context

> The Executive's own living doc: cross-cutting notes + the **direct-child
> roster**. Overwrite the state sections each pass. This is not the role
> bootstrap — that's `CEO.md` (stable). Fill in `<...>` as work begins.

Path: `docs/agents/executive/_context.md` | Last updated: <YYYY-MM-DD>

---

## Purpose & scope boundary        (stable)

Top-level orchestration of <project name>. Owns priorities, spawns managers,
integrates at milestones, carries product forks to the human.

## State of the work               (overwrite each pass; verify before trusting)

<where the whole effort stands right now — current phase, what just merged,
what's next. Run the verification command before trusting any claim here.>

## Decisions in force               (do not relitigate)

<cross-cutting decisions already locked — link ADRs where they live>

## Open decisions                   (empty if none)

<forks waiting on the human, each with your recommendation>

## Direct children                  (roster — one line each: path — purpose — status)

```
<scope-a>.md   — owns <area> [<task ids>] — <status>
<manager>/     — <subsystem> manager — <status>
```

Add a line when you spawn a child; update it when the child's scope is done.
Direct children only — walk down into a sub-manager's `_context.md` for depth.
Don't duplicate a child's spec; the child's doc is its own source of truth.
