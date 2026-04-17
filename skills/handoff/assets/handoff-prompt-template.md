# Handoff Prompt Template

Paste the fenced block below into a fresh Claude Code session. It's terse by design — the fresh agent reads `rules.md` and `decisions.md` directly from the repo (step 1 of its work). The prompt itself just names the files, confirms starting state, and lists phase-ephemeral context.

Why terse: inlining the full rules/decisions into every prompt bloats paste size (easily ~5% of a fresh session's context) and creates a stale-copy problem the moment either source file is edited. Named-file reads are cheaper and always current.

---

```
I'm resuming <project-name> on the <branch-name> branch.
<One sentence: what's done, what's next, mid-phase or starting fresh.>

Worktree / repo root: <absolute path>

READ THESE THREE FILES BEFORE ANY ACTION — do not skip:

1. <path-to-handoff-brief>
   — phase-specific state, ephemeral decisions, open questions
2. <path-to-rules.md>
   — durable hard rules (destructive-op guards, platform quirks, conventions)
3. <path-to-decisions.md>
   — durable architectural decisions with reasons

Then optionally:
- <path-to-plan-doc-or-PR>  (execution detail, what shipped)
- Memory entries (if the project uses auto-memory):
  <memory-name>, <memory-name>

Starting state:
- HEAD <SHA> on <branch>
- <baseline verification command>: all green (<N tests passing>)
- Routes / <URL>, / <URL> render (smoke-test: <status>)
- <any external ids the fresh session will need — collection id, project id, etc.>

Ephemeral decisions this phase (not in decisions.md — don't relitigate
without a real reason):
- <phase-specific decision with specifics>
- <phase-specific decision with specifics>

Known gotchas this phase (fix patterns):
- <gotcha> — <file or pattern>
- <gotcha> — <file or pattern>

Before executing any task, ASK the user:
  (a) Has <most-recent-work> been smoke-tested? (The brief says what has
      and what hasn't.)
  (b) Which task to start with. Default is <Task N>. Remaining tasks are
      in the brief; the priority order is:
        <N>.  <one-liner>
        <N+1>. <one-liner>
        ...

Report progress after each task: one short sentence + commit SHA.
Ask before deviating from the plan.

Start by <explicit first action — "asking the user (a) and (b)" or
"executing Task N" or "running verification commands">.
```

---

**Why this prompt is shaped the way it is:**

- Names the three mandatory files at the top in a MANDATORY-READ block. A fresh agent eager to start will see this before anything else.
- Restates ephemeral decisions + phase-specific gotchas inline. Those are per-phase, so they don't live in `rules.md` / `decisions.md`, but they matter for picking the first task.
- Lists starting-state assertions so the fresh session can verify it landed where expected.
- Does NOT inline `rules.md` or `decisions.md`. Those are one `Read` tool call away; regenerating them into the prompt wastes ~5% of context per handoff, doubles paste size, and goes stale the moment anyone edits the source.
- Explicit "ask the user (a) smoke-test status and (b) which task" — protects against eager agents starting work on unverified state.

**When to use this prompt:**

- Starting a new Claude Code session with this repo as the working directory.
- Context in the current session has gotten too long and the phase is partially done — update the brief (and `rules.md` / `decisions.md` if new durable content emerged), regenerate this prompt, paste into a new session.

**Author's reminder:**

Keep this prompt short. If it grows past ~100 lines, you're probably inlining content that belongs in the brief or the durable docs.
