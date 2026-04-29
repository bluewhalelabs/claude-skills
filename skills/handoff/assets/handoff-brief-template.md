# <Initiative or Topic> Handoff — Session Transfer Brief

**Date:** <YYYY-MM-DD>
**Surface:** `<surface>` (e.g. compass, studio, atlas, polygraph, boost, ad-analysis)
**Branch:** <git-branch-name>
**Status:** <one sentence — what's done, what's next, whether code has been written yet. Use `paused (<reason>)` or `superseded by <path>` if not active.>

> Durable rules live in two places, by scope:
> — `<handoff-dir>/<surface>/rules.md` — surface-specific rules
> — `<handoff-dir>/rules.md` — cross-cutting rules
> Durable decisions follow the same split (`<surface>/decisions.md` vs top-level `decisions.md`).
> The paste-in prompt names all four files but does **not** inline them. **They are not re-listed in this brief either.** The brief is initiative-scoped; durable content stays in its own file.
> Cross-surface initiatives: the brief lives under the **primary** surface and lists secondary surfaces touched in the execution path below.

---

## What a fresh session needs to read, in order

1. This doc — initiative-specific state and decisions
2. `<handoff-dir>/<surface>/rules.md` — surface-specific rules (most relevant first)
3. `<handoff-dir>/<surface>/decisions.md` — surface-specific decisions
4. `<handoff-dir>/rules.md` — cross-cutting rules
5. `<handoff-dir>/decisions.md` — cross-cutting decisions
6. `<path to plan or PR if relevant>` — execution detail
7. `<path to prior phase brief if still relevant>` — context for anything carried forward

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

These are scope choices or tradeoffs specific to **this initiative only**. They are not durable — they apply to this initiative's work and won't carry into the next without re-scoping.

(Durable decisions are in the surface and top-level `decisions.md` files; the prompt names them but does not inline them.)

1. **<Initiative-specific decision>.** <One-line reason.>
2. **<Initiative-specific decision>.** <One-line reason.>

If you catch yourself writing an entry here that's true across initiatives, classify it: surface-specific → `<surface>/decisions.md`; cross-cutting → top-level `decisions.md`. Tie-breaker: when in doubt, promote to cross-cutting.

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

**Cross-surface touchpoints (if any):** <list secondary surfaces this initiative touches and the file/route under each, e.g. "studio: `apps/unum/app/studio/api/library/route.ts` for federation"; delete this line if not applicable>

**Expected commit count (remaining):** <N>
**Expected final test count:** <N>

---

## New rules and decisions added this session

If this session produced content promoted to durable docs, list pointers here (not the content itself — that lives in the durable docs). Mark the destination so the fresh session knows where to look.

- Added rule: "<rule title>" → `<surface>/rules.md` _or_ top-level `rules.md`
- Added decision: "<decision title>" → `<surface>/decisions.md` _or_ top-level `decisions.md`

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
