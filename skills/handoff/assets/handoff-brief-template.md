# <Phase or Topic> Handoff — Session Transfer Brief

**Date:** <YYYY-MM-DD>
**Branch:** <git-branch-name>
**Status:** <one sentence — what's done, what's next, whether code has been written yet>

> Durable hard rules live in `<handoff-dir>/rules.md`.
> Durable architectural decisions live in `<handoff-dir>/decisions.md`.
> The paste-in prompt inlines both verbatim. **They are not re-listed in this brief.**

---

## What a fresh session needs to read, in order

1. This doc — phase-specific state and decisions
2. `<handoff-dir>/rules.md` — durable rules
3. `<handoff-dir>/decisions.md` — durable architectural decisions
4. `<path to plan or PR if relevant>` — execution detail
5. `<path to prior phase brief if still relevant>` — context for anything carried forward

---

## What already shipped (current state)

Be specific. List commits by SHA or one-line description. Name route paths. Name test counts. State verification status.

- <route/feature>: <one-line description>, commit <SHA>
- <route/feature>: <one-line description>, commit <SHA>

**Baseline verification:**
- `<typecheck command>` — <result>
- `<build command>` — <result>
- `<test command>` — <N tests passing>
- Visual verification of <route> confirmed by the user / pending

---

## Ephemeral decisions this phase

These are scope choices or tradeoffs specific to **this phase only**. They are not durable — they apply to this phase's work and won't carry into the next without re-scoping.

(Durable architectural decisions are in `decisions.md` and inlined in the prompt.)

1. **<Phase-specific decision>.** <One-line reason.>
2. **<Phase-specific decision>.** <One-line reason.>

If you catch yourself writing an entry here that's true across phases, move it to `decisions.md` instead.

---

## Key facts (snapshot at <date>)

Point-in-time facts the fresh session will need. These decay — re-verify before acting on them.

- <fact>
- <fact>
- <fact>

---

## The execution path at a glance

| # | Task | Notes |
|---|---|---|
| 1 | <task> | <one-line note> |
| 2 | <task> | <one-line note> |
| 3 | <task> | <one-line note> |

Point at the plan doc for per-task details — don't re-describe them here.

**Expected commit count (remaining):** <N>
**Expected final test count:** <N>

---

## New rules and decisions added this session

If this session produced content promoted to `rules.md` or `decisions.md`, list pointers here (not the content itself — that lives in the durable docs). Helps the fresh session know what just changed.

- Added rule: "<rule title>" → `rules.md`
- Added decision: "<decision title>" → `decisions.md`

---

## Risks to watch for

Concrete pitfalls the fresh session might hit. Name the file or task where each risk lives.

1. **<Risk name>.** <What to watch for, and how to handle it if it happens.>
2. **<Risk name>.** <What to watch for, and how to handle it if it happens.>

---

## Open questions the fresh session should NOT decide unilaterally

Things with real tradeoffs the user needs to weigh. The fresh session should ask, not decide.

- <question>
- <question>

---

## Environment state when this doc was written

- **Branch:** <name>, HEAD is <SHA> (<one-line commit subject>)
- **Working tree:** <clean, or note what untracked files exist and why>
- **Tests:** <N passing>
- **Routes:** <list the working routes>
- **Key scripts that must continue to exist:** <list them>
- **Env vars required:** <names only, never values>
