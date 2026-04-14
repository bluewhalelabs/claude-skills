---
name: handoff
description: Use when a long-running work session needs to transfer state to a fresh session. Triggers on phrases like "create a handoff", "prepare a handoff", "hand this off", "context is getting long", "wrap this up so we can continue in a new session", "give me a prompt for the next session", or any time the user signals they want to pause mid-flight on a multi-phase project and resume later. Produces a two-file handoff: (1) a detailed brief doc cataloguing current state, locked-in decisions, risks, and hard rules, and (2) a paste-in prompt the user copies into a fresh session that restates all key decisions inline so the next session doesn't have to rediscover them. Use this proactively when you notice the context window is filling up during multi-session work — don't wait for an explicit request if the user is clearly about to run out of runway.
---

# Session Handoff

When a long-running work session needs to transfer its state to a fresh session, produce a two-artifact handoff: a brief doc + a paste-in prompt. The fresh session reads the prompt first (it's self-contained) and then the brief (it's the depth reference).

## Why two artifacts, not one

Each plays a different role and the redundancy is the point:

- **The brief** is the reference doc. Fresh session reads it after the prompt tells it to. It contains full detail — ordered reading list, current state, locked-in decisions with reasons, point-in-time facts, risks, hard rules, open questions.
- **The paste-in prompt** is what the user literally pastes into a new Claude Code session. It must be self-sufficient: a fresh agent must be able to act on the prompt alone without having read anything else. The prompt restates the essential decisions inline so the fresh agent can't claim "I didn't see that."

A single doc can't do both jobs. A prompt without a brief leaves details out. A brief without a prompt requires the fresh session to fish for itself. Both are cheap to produce once you have the facts, so write both.

## When to use this skill

Trigger it any time the user signals one of these:

- Explicit ask: "create a handoff", "prepare a handoff", "make a handoff doc", "hand this off", "give me a prompt for the next session"
- Context pressure: "context is getting long", "this conversation is huge", "let's wrap up so we can continue later"
- Mid-phase pause: user has finished one phase of a multi-phase project and wants to pause before the next phase

Use it proactively if you notice the session is clearly approaching context limits while the user is deep in multi-phase work. Don't wait for an explicit ask if the runway is about to run out.

## Process

The whole process has six steps. Do them in order. Don't skip the "verify current state" step — it's what makes the brief factually correct at write time.

### 1. Find where handoff docs should live

Look for an existing convention in the repo:

- `docs/plans/` (most common for multi-phase work)
- `docs/handoffs/`
- `docs/` (flat)
- `~/.claude/plans/` (user-level plans)

Run `git log --oneline -20 -- 'docs/**'` or list the docs directory to see where prior plan or handoff docs live. Match the existing convention. If there's no existing pattern, ask the user where to put them — don't invent a new location.

### 2. Scan the conversation for locked-in decisions

Read back through the current session and find every place where:

- The user explicitly approved something ("yes, go with A", "that sounds right")
- The user explicitly rejected something ("no, don't do X")
- The user corrected course ("actually, let me clarify...")
- A tradeoff was discussed and a specific side was chosen

These are the locked-in decisions. Quote them faithfully. Don't over-generalize them into abstract principles that lose the specifics — the specifics are what future you needs.

For each decision, capture the *reason* too. A decision without a reason will get re-debated.

### 3. Verify current state

Before writing facts into the brief, confirm they're true *right now*:

- Branch name: `git branch --show-current`
- Working tree: `git status --short`
- Recent commits: `git log --oneline -20` or scope to the current phase
- Tests (if applicable): run the test suite and record the pass count
- Build (if applicable): run the build and confirm it's green
- Routes/features working (if applicable): check with whatever verification the project uses

The brief must be factually accurate at write time. Don't write "19 tests passing" unless you just saw 19 tests pass.

### 4. Write the brief

Use `assets/handoff-brief-template.md` as a starting point. Fill in every section that applies. Drop sections that don't apply — empty sections waste the reader's time.

Key sections:

- **What to read in order** — list the docs the fresh session should read, in order, with one-line descriptions of what each provides. The brief itself should be first.
- **Current state** — what already shipped. Specific: commit SHAs, route paths, test counts, verification status.
- **Decisions locked in this session** — numbered list with reasons. Say explicitly "these are not up for debate without a real reason" so a fresh session doesn't reopen them.
- **Key facts (snapshot)** — point-in-time assertions about external systems, DB state, file layouts. Note that these decay — tell the reader to re-verify before acting on them.
- **Execution path at a glance** — short table of remaining tasks or phases, with one-line descriptions. Point at the plan doc for details instead of re-describing them.
- **Hard rules carried forward** — numbered list of things the fresh session would otherwise default-break (e.g., "don't start dev servers", "don't touch X/Y"). Each rule should have a brief reason.
- **Risks to watch for** — concrete pitfalls with the file or task where each risk lives.
- **Open questions the fresh session should NOT decide unilaterally** — items with real tradeoffs that the user needs to weigh. Name them explicitly so the next session knows to ask.
- **Environment state when this doc was written** — Git HEAD, test count, routes, relevant scripts, env vars (never log their values).

### 5. Write the paste-in prompt

Use `assets/handoff-prompt-template.md` as a starting point. The prompt is a single fenced code block followed by a short explanation of when to use it.

Structure inside the fenced block:

1. One-sentence context: what project, what branch, what state
2. Numbered list of docs to read in order, with exact paths
3. Memories to check (if the project uses auto-memory)
4. Confirmation of the starting state (test count, routes, build status) — so the fresh session can verify it landed in the expected state
5. **The locked-in decisions, restated inline** — not "see the brief" but the actual list of decisions. Include reasons.
6. **Hard rules, restated inline** — not "see the brief" but the actual rules.
7. Known gotchas with the file or fix pattern for each
8. Explicit starting instruction: "Start with Task N" or "Start by running the pre-flight checks"

The prompt must be self-contained. A fresh session should be able to act on the prompt alone without reading any other file — the other files are for depth, not essentials.

### 6. Commit both files and always surface the prompt inline

Commit with a single message like `docs(<phase>): handoff brief and paste-in prompt for <topic>`. List both files.

**Always paste the fenced prompt block directly into your final reply**, in addition to telling the user where the files live. This is a standing user preference — the prompt belongs in the terminal every time, not just when the user explicitly asks for it. Put the paste-in prompt first in the reply (so it's easy to copy from the scrollback), then a short note with the file paths and commit SHA.

## Anti-patterns

Things that look right but produce weak handoffs:

- **Vague summaries.** "We made good progress on the auth layer" is useless. Say exactly what shipped: route paths, commit SHAs, test counts, files added.
- **Decisions without reasons.** Every locked-in decision needs a one-line reason. Otherwise the fresh session will re-debate it.
- **Collapsing the two artifacts into one.** Don't write just a prompt, or just a brief. They serve different purposes.
- **"Read the brief for details."** If a decision is important enough to mention in the prompt, put it in the prompt. Don't chain the next session through multiple reads for essentials.
- **Skipping the "what not to decide" section.** Fresh sessions are eager and will "fix" open questions if not told to ask first. Name them explicitly.
- **Not verifying current state.** Don't write "tests passing" without running them. Don't write "routes working" without checking the build.
- **Inventing a new location.** If the project already has a `docs/plans/` convention, use it. Don't drop a new file in a new place.
- **Embedding the full plan inside the brief.** If a plan doc already exists at its own path, point at it. Re-including it makes the brief stale the moment the plan is updated.
- **Over-abstracting the decisions.** "We decided to use a hybrid approach" hides the specifics the next session actually needs. Write the decision concretely: "Pipeline data reads from `report_run_outputs` with `output_type='pipeline-graph'`, backfill via `scripts/backfill-pipeline-graph.ts`, URL is `/reports/pipeline/[edition]` with edition as a plain number."

## Templates

Two templates live in `assets/`:

- `assets/handoff-brief-template.md` — fill-in structure for the brief doc
- `assets/handoff-prompt-template.md` — fill-in structure for the paste-in prompt

Read them, adapt section-by-section to the specific project, and delete any sections that don't apply rather than filling them with placeholders.
