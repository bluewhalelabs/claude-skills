# <Phase or Topic> Handoff — Session Transfer Brief

**Date:** <YYYY-MM-DD>
**Branch:** <git-branch-name>
**Status:** <one sentence — what's done, what's next, whether code has been written yet>

---

## What a fresh session needs to read, in order

1. This doc — session-transfer brief with current state, locked-in decisions
2. <path to plan doc if one exists>
3. <path to prior handoff doc if one exists>
4. <path to high-level plan or architecture doc if relevant>

**Memories to check:**
- <list any auto-memory entries the fresh session should load for context>

---

## What already shipped (current state)

Be specific. List commits by SHA or one-line description. Name route paths. Name test counts. State verification status.

- <route/feature>: <one-line description>, commit <SHA>
- <route/feature>: <one-line description>, commit <SHA>

**Baseline verification:**
- `<typecheck command>` — <result>
- `<lint command>` — <result>
- `<build command>` — <result>
- `<test command>` — <N tests passing>
- Visual verification of <route> confirmed by the user / pending

---

## Decisions locked in during this session

These are not up for debate without a real reason. If the fresh session wants to reopen any of these, it should ask first.

1. **<Decision>.** <One-line reason.>
2. **<Decision>.** <One-line reason.>
3. **<Decision>.** <One-line reason.>

(Keep each decision specific. "Use a hybrid data layer" is too abstract — say what that means concretely: "Pipeline data reads from `report_run_outputs` where `output_type='pipeline-graph'`, backfill via `scripts/backfill-pipeline-graph.ts`, route at `/reports/pipeline/[edition]` with edition as a number like `2`.")

---

## Key facts (snapshot at <date>)

Point-in-time facts about the DB, external systems, file layouts, or anything the fresh session will need. These decay — the fresh session should re-verify before acting on them.

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

**Expected commit count:** <N>
**Expected final test count:** <N>

---

## Hard rules carried from prior phases

These are things the fresh session would otherwise default-break. Restate them explicitly.

1. **<Rule>.** <Brief reason.>
2. **<Rule>.** <Brief reason.>
3. **<Rule>.** <Brief reason.>

---

## Risks to watch for

Concrete pitfalls the fresh session might hit. Name the file or task where each risk lives.

1. **<Risk name>.** <What to watch for, and how to handle it if it happens.>
2. **<Risk name>.** <What to watch for, and how to handle it if it happens.>

---

## The handoff prompt to paste into a fresh session

A ready-to-copy prompt lives at <path to handoff-prompt file>. Open it, copy the fenced code block, paste into a new Claude Code session with this repo as the working directory.

---

## Open questions the fresh session should NOT decide unilaterally

Things with real tradeoffs the user needs to weigh. The fresh session should ask, not decide.

- <question>
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
