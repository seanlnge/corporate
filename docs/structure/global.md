# Global context — all agents read this once

> Project charter, current status, global rules, and the verification command.
> Every agent (executive, manager, worker) reads this first. The Executive owns
> the status table. Fill in every `<...>` before first use.

Path: `docs/structure/global.md` | Last updated: <YYYY-MM-DD>

---

## Charter

**Project:** <project name> — <one-line description of the system>.

**v1 goal:** <the single outcome that means v1 is done>.

**Out of scope for v1:** <what we are deliberately not building yet>.

## Status

| Phase | Status | One-line summary |
|-------|--------|------------------|
| <Phase 0 name> | <not started / in progress / done> | <summary> |
| <Phase 1 name> | <not started / in progress / done> | <summary> |

## Global rules

- <rule every agent must follow — e.g. language/style, commit conventions>
- <off-limits paths / systems no agent touches without escalation>
- <how to reference tasks — tracker/board link + ID format>

## Verification command

```
<the single command that proves the build/tests are green — e.g. `npm test`>
```

Run this before trusting any doc's claimed state and before reporting work done.
On mismatch between a doc and this command's output, **reality wins** — fix the
doc's state section first.
