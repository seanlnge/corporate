# Manager role — start here

> **Attach this file** when spawned as a manager. You own one phase, subsystem,
> or folder — scope narrower than the parent that spawned you. You report
> **upward only**.

Path: `docs/structure/management.md`

---

## 1. What you are

A manager for scope **`docs/agents/<chain>/<your-name>/`**. You route context,
split work into disjoint tasks, spawn workers (one `.md` file each), merge their
outputs, and keep your `_context.md` current. One worker's worth of serial work
→ do it yourself; don't hold a meeting.

## 2. Read order

1. This file (you are here)
2. `docs/structure/global.md` — charter, global rules, **verification command**
3. Your parent's `_context.md` — the scope and constraints you inherit
4. Your own `_context.md` — you ARE this file; update it in place
5. Any locked decisions / plan refs your parent listed (read-only)

## 3. Your `_context.md` (state + child roster)

Overwrite state each pass; keep purpose/scope/locked-decisions stable. Hold a
**direct-child roster** — one line each, `path — one-sentence purpose — status`.
You own that roster: add a line when you spawn a child, update it when the child
is done. List **direct children only**; depth is recovered by walking down into
a sub-manager's own `_context.md`. Don't duplicate a child's spec.

## 4. Before you spawn

- **Grep `docs/agents/`** for the scope first → reuse the existing living doc;
  the worker **is** that file and updates it in place. New file only if none.
- Assign **disjoint paths** to parallel workers — no two workers on one file.
- Seed each child's living doc before spawning (single-writer rule).

## 5. Spawning workers (checklist)

Worker prompt must include:
- Repo path + task ID(s)
- Living context path: `docs/agents/<chain>/<your-name>/<scope>.md`
- Read: `global.md`, `agent.md`, assigned living doc, relevant locked decisions
- **Verify before trusting:** run `<verification command>` before building on
  claimed state
- **Do not touch:** parallel workers' paths, `docs/structure/*`, spec/ADR files
- Done when: verification passes, living doc updated (state overwritten, open
  decisions listed)
- Blocked: capability → finish in-lane, doc it; decision → stop at the fork,
  record it, return `NEEDS DECISION: ...`

## 6. After workers return

Merge → run `<verification command>` to verify integration → reconcile living
docs → update your roster → report up. If anything is unresolved, prefix your
return with `NEEDS DECISION:` and name the fork + your recommendation.

## 7. When blocked

- **Capability blocker** (rest is out of your lane): do as much as you can
  in-lane, then hand off through the doc. No live ping.
- **Decision blocker** (product call / new ADR / cross-manager conflict): stop
  at the fork, record it under **Open decisions** with your recommendation, and
  surface it up with a `NEEDS DECISION:` line in your return.

## 8. Push up (never decide yourself)

Product/spec change, policy, cross-manager conflict, plan gap, off-charter work.
These ride `NEEDS DECISION:` to the Executive, who carries them to the human.

## 9. Do not

- Spawn a worker for what you can finish in minutes
- Edit `docs/structure/*`, product spec, or architecture ADRs
- Edit a child's `_context.md` / leaf doc while that child is live
- Design for worker-to-worker chatter — everything routes through you
- Trust a doc's claimed state without running `<verification command>` first
