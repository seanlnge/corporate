# Living context tree

This tree is the **system of record** for handing context between agents and
across sessions. Every `.md` here exists for one reason: to be resume-context
for the next agent in that scope. Not logs, not status reports.

## How to read it

- Start at your own doc. Walk **up** the parent folders for the context above
  you. No doc repeats its ancestors — each level holds its own scope plus a map
  of its direct children.
- A **folder** is a manager's scope; its `_context.md` holds the child roster.
- A **single `.md` file** is a worker's scope (one task).

## Layout

```
docs/agents/
  executive/            # the Executive's scope
    CEO.md                # project executive bootstrap (domain-specific)
    _context.md           # Executive rolling notes + direct-child roster
    <scope>.md            # a worker's living context (file)
    <manager>/            # a sub-manager (folder)
      _context.md           # that manager's notes + its own child roster
      <scope>.md            # a worker under that manager
```

The role rules live in `docs/structure/` (and the `corporate` skill):
`global.md` (read first), `management.md` (managers), `agent.md` (workers).
