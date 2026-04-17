# Handoff Prompt Template

Paste the fenced block below into a fresh Claude Code session. It is **ruthlessly terse** — hard cap ≤50 lines, aim for 30-40. The fresh agent reads `rules.md`, `decisions.md`, and the phase brief directly from the repo; the prompt just gets it oriented.

**If your prompt is longer than 50 lines, you are inlining content that belongs in the brief.** Task queues, shipped-work summaries, file-by-file annotations, and durable rules/decisions all go in the brief or the durable docs — never in this prompt.

---

```
I'm resuming <project-name> on <branch-name>. <One sentence: what phase, roughly where we are.>

Worktree / repo root: <absolute path>

READ THESE THREE FILES BEFORE ANY ACTION — do not skip:

1. <path-to-handoff-brief>
2. <path-to-rules.md>
3. <path-to-decisions.md>

Starting state:
- HEAD <SHA> on <branch> (pushed: yes/no)
- <build/typecheck/test status in one line>
- <last smoke-test status — what was verified, what wasn't>

Ephemeral decisions this phase (don't relitigate; brief has the rest):
- <phase-specific decision — one line>
- <phase-specific decision — one line>

Known gotchas (fix patterns):
- <gotcha> — <file>
- <gotcha> — <file>

Before executing, ASK the user:
  (a) Smoke-test status of <most-recent unverified work>?
  (b) Which task to start with? Default <Task N>. See brief §execution path for the full queue.

Report progress after each task: one short sentence + commit SHA. Ask before deviating.
```

---

**Length check before emitting:**

- Count the lines in your prompt. Over 50? Cut.
- Did you list more than 3 ephemeral decisions? Cut to the 3 that matter most for picking the first task; the rest stay in the brief.
- Did you list more than 3 gotchas? Same treatment.
- Did you enumerate remaining tasks with descriptions? Delete them. The brief has the task queue; the prompt just names a default.
- Did the opening "context sentence" become a paragraph of shipped commits? Collapse it. "Phase 2 has ~N commits on top" is enough.
- Did you add an "optional files to read" block beyond the three mandatory? Move it to the brief.

**When to use this prompt:**

- Starting a fresh Claude Code session with this repo as the working directory.
- Mid-session context pressure — regenerate brief + prompt, paste into a new session.

**Why this prompt is shaped the way it is:**

- Three mandatory reads at the top, named and ordered, no summaries. A fresh agent sees this first.
- Starting-state lines so the agent can verify it landed where expected before acting.
- Only 3 ephemeral bullets each for decisions and gotchas — the absolute minimum to pick the first task. Deeper context is one `Read` call away in the brief.
- Explicit "ask the user (a) and (b)" instruction so eager agents don't start work on unverified state.
- No task queue, no shipped-work summary, no durable content. Those all live in files the agent will read.
