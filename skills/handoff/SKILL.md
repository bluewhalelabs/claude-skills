---
name: handoff
description: Use when a long-running work session needs to transfer state to a fresh session. Triggers on phrases like "create a handoff", "prepare a handoff", "hand this off", "context is getting long", "wrap this up so we can continue in a new session", "give me a prompt for the next session", or any time the user signals they want to pause mid-flight on a multi-phase project and resume later. Maintains a self-contained handoff ecosystem in the project — two durable docs (`rules.md`, `decisions.md`) that outlive any single phase, plus a per-phase brief and paste-in prompt. The prompt inlines the durable docs verbatim so a fresh agent gets everything in one paste, but the durable docs are the single source of truth and don't get duplicated across phase briefs. Use proactively when context is filling up mid-multi-phase work — don't wait for an explicit ask.
---

# Session Handoff

When a work session needs to transfer state to a fresh session, produce a handoff into a **self-contained ecosystem of four artifacts** that lives entirely in one directory. Two of the four are durable (edited in place, outlive phases); two are ephemeral (tied to the current phase and deleted when it lands).

## Why four artifacts

Three problems have to be solved simultaneously:

1. **Durable content (hard rules, architectural decisions) gets repeated across handoffs and drifts across copies.** Duplication is the failure mode.
2. **Phase-specific state decays fast.** Current HEAD, what shipped this week, who's doing what — obsolete in days.
3. **A fresh agent shouldn't hunt for context.** Name the files it must read, and it will. Don't make it guess.

The four-artifact layout:

- **`rules.md`** — durable hard rules (e.g. "never push to prod without approval", "don't start dev servers", forbidden directories, platform quirks). **Single source of truth.** Edited in place when a new rule emerges. Outlives every phase.
- **`decisions.md`** — durable architectural decisions with reasons (e.g. "data layer uses view X, not join table Y", "predicate JSON is versioned"). Append-mostly. Outlives every phase. Revised only when a decision is explicitly reversed.
- **`<date>-<topic>-handoff.md`** — ephemeral phase brief. Current state, what shipped this phase, ephemeral decisions (scope choices specific to this phase), risks, open questions. Deleted or marked superseded when the phase's PR merges.
- **`<date>-<topic>-handoff-prompt.md`** — the paste-in prompt. **Ruthlessly terse — hard cap ≤50 lines, aim for 30-40.** Names the three docs the fresh agent must read and restates almost nothing inline. The brief carries the detail; the prompt just gets the agent oriented and pointed at the right files. **Does NOT inline `rules.md` or `decisions.md`** — regenerating them per handoff doubles paste size and goes stale. **Does NOT re-enumerate the task queue, re-summarize shipped work, or re-list files beyond the three mandatory reads** — all of that is in the brief.

Everything lives in one directory under the project (e.g. `docs/handoffs/`). The skill never scatters content into `CLAUDE.md`, memory files, or project-specific conventions — those vary by project. The handoff ecosystem is portable: same layout works in any repo.

## The context-capture philosophy

The markdown files are where context accumulates. The **prompt is just an index card** pointing at them. When you're tempted to add "one more thing" to the prompt — a commit summary, a gotcha, a task detail — the right move is almost always to put it in the brief (or promote it to the durable doc) and leave the prompt alone.

A handoff's quality is measured by two things:

1. **Is the detail captured somewhere durable?** (It should be — in the brief, `rules.md`, or `decisions.md`.)
2. **Is the prompt short enough that pasting it doesn't burn 5% of the fresh session's context?** (Target: ≤50 lines.)

Both can be true at the same time only if the markdown files are doing the heavy lifting.

## Size must go down over time

The ecosystem is lossy by design. Every handoff cycle, some content leaves the system:

- Tasks that landed disappear from the task queue.
- Gotchas that got fixed disappear from the gotchas list.
- Open questions that got answered either disappear (if the answer was phase-scoped) or promote to `decisions.md` (if durable).
- Ephemeral decisions that were superseded get deleted.

**If the brief is bigger than it was at the last handoff, something is wrong.** Either a genuinely new phase started (expected) or old content is accumulating (bad — prune it). The durable docs can grow, but slowly, and only with net-new durable content — not by accumulating ephemera that was never promoted properly.

The pruning step (step 7) is where this discipline is enforced. Do not skip it.

## When to use this skill

Trigger on any of these:

- Explicit ask: "create a handoff", "prepare a handoff", "hand this off", "give me a prompt for the next session"
- Context pressure: "context is getting long", "let's wrap up so we can continue later"
- Mid-phase pause: one phase done, pausing before the next

Use it proactively if you notice the session is clearly approaching context limits mid-multi-phase work. Don't wait for an explicit ask if the runway is about to run out.

## Process

Seven steps. Don't skip the "verify current state" step or the "classify durable vs ephemeral" step — those are what keep the ecosystem working over time.

### 1. Locate (or initialize) the handoff directory

Look for an existing convention. If you've written a handoff in this repo before, it's already there — typically one of:

- `docs/handoffs/`
- `docs/plans/` (older convention, may mix with planning docs)
- `docs/superpowers/plans/` (seen in some repos)

List the docs dir and look for prior `*-handoff*.md` files. Match the existing location.

If this is the **first** handoff in the project, ask the user where they want the directory. Default suggestion: `docs/handoffs/`. Don't invent a location silently.

Once you have the directory, check for `rules.md` and `decisions.md`:

- **Both exist** → read them. You'll inline them in the prompt later.
- **Neither exists** → first-time setup. Ask the user if they want you to initialize from `assets/rules-template.md` and `assets/decisions-template.md`. If yes, draft initial content from this session's conversation and get user approval before saving.
- **One exists** → initialize the missing one the same way.

### 2. Classify decisions and rules: durable vs ephemeral

Scan the current session for every place the user:

- Approved something ("yes, go with A")
- Rejected something ("no, don't do X")
- Corrected course ("actually, let me clarify...")
- Picked a side when a tradeoff was discussed

For each decision, classify it:

- **Durable** — true across phases, not specific to this week's task. Examples: "the data layer uses `label_assignments` polymorphic + collection-scoped", "predicate JSON is versioned, predicates themselves aren't", "colons only allowed on labels attached to a dimension". These belong in `decisions.md` — **append them now** with the date and reason.
- **Ephemeral** — specific to this phase's scope. Examples: "PR #76 got merged in", "unanimous-only for this backfill", "flat predicates only in v1 of the rules UI". These stay in the phase brief.

The same classification applies to **hard rules** that come up in conversation. If the user says "never commit without asking" and that rule isn't in `rules.md` yet, add it there — don't re-state it in the brief where it'll get duplicated next phase.

Capture the *reason* for every decision and rule. A decision without a reason gets re-debated.

### 3. Verify current state

Before writing facts into the brief, confirm they're true *right now*:

- Branch: `git branch --show-current`
- Working tree: `git status --short`
- Recent commits: `git log --oneline -20` or scoped to the current phase
- Tests (if applicable): run the suite, record the pass count
- Build (if applicable): run it, confirm green
- Routes/features: verify with whatever verification the project uses

Don't write "19 tests passing" unless you just saw 19 tests pass. Don't write "routes working" unless the build was green at this HEAD.

### 4. Update `rules.md` and `decisions.md` if the session produced new ones

Any durable rule or decision identified in step 2 gets appended to the right file **before** you write the phase brief. This way:

- The brief doesn't duplicate them.
- Future handoffs see the up-to-date single source.
- Git history on `rules.md` / `decisions.md` shows when each rule/decision entered the project.

Use ISO dates (`## 2026-04-16` or similar) so readers can see when a rule was introduced. Don't rewrite history — append new entries at the bottom (or in a dated section).

### 5. Write the phase brief

Use `assets/handoff-brief-template.md`. Key sections:

- **What to read in order** — the brief itself, then `rules.md`, then `decisions.md`, then the PR or plan doc. Don't re-list every durable rule; the durable docs are concise enough to read directly.
- **What already shipped this phase** — specific: commit SHAs, route paths, test counts, verification status.
- **Ephemeral decisions this phase** — the phase-scoped tradeoffs. Say explicitly "durable decisions live in `decisions.md` and are inlined in the prompt."
- **Key facts (snapshot)** — point-in-time DB state, ids, counts. Note these decay.
- **Execution path at a glance** — remaining tasks or phases, one line each. Point at the plan doc for details.
- **Risks to watch for** — concrete pitfalls with the file or task where each lives.
- **Open questions the fresh session should NOT decide unilaterally** — real tradeoffs for the user to weigh.
- **Environment state** — Git HEAD, test count, routes, env var *names* (never values).

**Do NOT re-list hard rules or durable decisions in the brief.** The brief points at `rules.md` / `decisions.md`; the prompt tells the fresh agent to read them.

### 6. Write the paste-in prompt

Use `assets/handoff-prompt-template.md`. The prompt is a single fenced code block followed by a short explanation of when to use it.

**Hard cap ≤50 lines, aim for 30-40.** The prompt's job is to orient a fresh agent and point at the three files — nothing more. Detail belongs in the brief. If the prompt is creeping past 50 lines, you're inlining content that belongs elsewhere; move it out.

The prompt does NOT inline `rules.md` or `decisions.md` — the fresh agent reads them directly as step 1.

**Prompt structure (lean version):**

1. **One sentence** of context: project, branch, where we are. Not a paragraph. Do not enumerate shipped commits here — "Phase 2 has ~19 commits on top" is enough; the brief has the list.
2. **Mandatory-read block** (explicit, bold): "READ these three files before any action" — brief, `rules.md`, `decisions.md`. Just paths, no per-file summaries.
3. **Starting state** — 3-5 bullets max: HEAD SHA, build/typecheck status, last smoke-test status. External ids only if the fresh session cannot find them via a single command.
4. **Ephemeral decisions — 3 bullets max.** Only the handful the agent needs before picking the first task. Longer lists go in the brief.
5. **Known gotchas — 3 bullets max**, each one line with a file path. Longer lists go in the brief.
6. **Explicit starting instruction**: "Start by asking the user X" or "Start with Task N." Reference the brief for the task queue — **do not re-enumerate tasks in the prompt**; even a five-task list with descriptions blows the budget.

**What the prompt does NOT contain:**

- The remaining task list with descriptions. (Brief has the table. Prompt says "see brief §execution path".)
- A second "optional files to read" block beyond the three mandatory. (Brief lists them.)
- Memory-file bullets more than 2-3 entries. (If there are many, say "check memory index" and move on.)
- Any summary of what shipped this phase. (Brief has it.)
- Any hard rule or architectural decision. (Those live in `rules.md` / `decisions.md`.)

The prompt is self-contained for *starting*: it tells the agent WHICH files to read and WHAT to do first. Everything else is one `Read` call away.

### 7. Prune before committing

The ecosystem works only if the files shrink as items move out of scope. Every handoff is also a cleanup pass. Do the pruning **before** you commit.

**Phase brief (the one you just wrote or updated):**
- Tasks that have landed → delete them from the execution path. The brief describes what's next, not what's done. (Shipped work goes in "What already shipped" as a one-liner with SHA; it gets culled on the *next* handoff.)
- Open questions that have been answered → delete, and if the answer is durable, promote it to `decisions.md`.
- Gotchas fixed in code → delete. If the fix needs to stay top-of-mind (e.g. requires a specific pattern elsewhere), promote it to `rules.md`.
- Ephemeral decisions from prior phases that are now moot → delete.
- "Key facts (snapshot)" that have changed → refresh or delete. Point-in-time data decays fast.

**`rules.md`:**
- Rule no longer applies (refactored away, tool changed) → delete it.
- Two rules say the same thing in different words → merge.
- Rule that turned out to be situational rather than absolute → either sharpen it (add the condition) or move the judgment into `decisions.md`.

**`decisions.md`:**
- Decision reversed → delete the old entry and write the reversal with its reason. Don't leave stale entries with "~~strikethrough~~"; that just grows the file.
- Decision that's become load-bearing convention in the code → keep, but trim the exploration notes down to the decision + one-line reason.

**Prior phase's brief and prompt:**
- Prior phase PR merged → delete the prior `*-handoff.md` and `*-handoff-prompt.md`. Git log preserves history.
- Prior phase still in flight (not merged) → add a one-line `**Superseded by:** <new-brief-path>` banner at the top of the prior brief. The prior brief stays readable but clearly marked.

Never delete `rules.md` or `decisions.md`. They're durable. But do keep them lean — a 500-line rules file gets skipped by fresh agents.

**Size check:** if any of the four files has grown compared to the prior handoff, justify it. Net-new durable rules or decisions are fine; accumulated ephemera is not.

Commit all affected files (durable edits + pruned entries + new brief + new prompt + prior-brief deletion/supersession) in a single commit: `docs: handoff for <topic>` (note any significant prunes in the body, e.g. "pruned 4 resolved gotchas, promoted 2 ephemerals to decisions.md").

**Always paste the fenced prompt block into your final reply**, in addition to telling the user where the files live. This is a standing user preference — the prompt belongs in the terminal every time. Put the prompt first (easy to copy from scrollback), then a short note with the file paths and commit SHA.

## Anti-patterns

Things that look right but produce weak or drift-prone handoffs:

- **Duplicating hard rules in every phase brief.** If the same rule is in this brief AND the prior one AND `rules.md`, you'll have three versions that drift. Keep it in `rules.md` only; the prompt names the file, it doesn't inline the content.
- **Re-enumerating the task queue in the prompt.** The brief has it. The prompt's job is to get the agent to *read* the brief, not to recreate it. Five tasks with one-line descriptions is already ~15 lines of prompt budget gone.
- **Expanding the one-sentence context into a paragraph of shipped work.** "Phase 2 has stacked ~19 commits since <date>" is enough. The fresh agent will see the commit log and the brief.
- **Inventing project-specific locations for rules.** Don't put hard rules in `CLAUDE.md`, `AGENTS.md`, memory files, `docs/engineering/standards.md`, etc. The handoff ecosystem is self-contained. Other conventions vary across projects; this one doesn't.
- **Treating the phase brief as an archive.** It's ephemeral. Delete it when the phase lands. Durable content lives in `rules.md` / `decisions.md`.
- **Collapsing the four artifacts into one.** Each plays a different role; collapsing loses either DRY (durable content drifts) or paste-self-sufficiency (prompt requires extra reads).
- **Vague summaries.** "Made good progress on the auth layer" is useless. Say exactly what shipped: SHAs, route paths, test counts, files added.
- **Decisions without reasons.** Every entry in `decisions.md` needs a one-line reason. Otherwise the fresh session re-debates it.
- **Skipping the classify step.** If you don't separate durable from ephemeral, everything ends up in the brief and `rules.md` / `decisions.md` get ignored. The whole ecosystem depends on the discipline.
- **"Read the brief for details"** in the prompt. If it matters for starting, inline it in the prompt. The brief is for depth, not essentials.
- **Skipping the "what not to decide" section.** Fresh sessions will eagerly "fix" open questions if not told to ask first.
- **Not verifying current state.** Don't write "tests passing" without running them.
- **Over-abstracting decisions.** "We chose a hybrid approach" hides the specifics. Write concretely: "Pipeline data reads from `report_run_outputs` where `output_type='pipeline-graph'`, backfill via `scripts/backfill-pipeline-graph.ts`, URL `/reports/pipeline/[edition]` with edition as a plain number."

## Templates

Four templates live in `assets/`:

- `assets/rules-template.md` — starter structure for a project's durable rules (initialized once, edited in place)
- `assets/decisions-template.md` — starter structure for durable architectural decisions (append-mostly)
- `assets/handoff-brief-template.md` — fill-in structure for each phase's brief
- `assets/handoff-prompt-template.md` — fill-in structure for each phase's paste-in prompt

Adapt section-by-section to the specific project. Delete sections that don't apply rather than filling them with placeholders.
