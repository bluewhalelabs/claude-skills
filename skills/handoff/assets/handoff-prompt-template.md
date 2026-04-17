# Handoff Prompt Template

Paste the fenced block below into a fresh Claude Code session. It is self-contained — the agent does not need to read any other file to start executing. The durable sections (rules, decisions) are **inlined verbatim** from `<handoff-dir>/rules.md` and `<handoff-dir>/decisions.md` at emit time, so the fresh agent sees the current state without a second hop.

---

```
I'm resuming <project-name> on the <branch-name> branch.
<One sentence: what's done, what's next, whether this is mid-phase or starting fresh.>

Worktree / repo root: <absolute path>

Read these in order before doing anything:

1. <path-to-handoff-brief>
   — phase-specific state and ephemeral decisions
2. <path-to-rules.md>
   — durable hard rules (also inlined below)
3. <path-to-decisions.md>
   — durable architectural decisions (also inlined below)
4. <path-to-plan-doc-or-PR>
   — execution detail / authoritative source of what shipped

Then check these memories (if the project uses auto-memory — otherwise skip):
- <memory-name>
- <memory-name>

Current state:
- <branch> branch, HEAD <SHA>
- <baseline verification command>: all green (<N tests passing>)
- Routes / <URL>, / <URL>, / <URL> render (typecheck-clean; user smoke-test: <status>)
- <any collection ids / external system ids the fresh session will need>

Before executing any task, ASK:
  (a) Has the latest phase been smoke-tested? If not, pause and let the user
      verify before layering more work.
  (b) Which task to start with. Default is <Task N>. Remaining task list:
        <N>. <task one-liner>
        <N+1>. <task one-liner>
        ...

=== DURABLE HARD RULES (from rules.md — inlined verbatim) ===

<PASTE THE FULL CONTENTS OF rules.md HERE AT EMIT TIME>

=== DURABLE ARCHITECTURAL DECISIONS (from decisions.md — inlined verbatim) ===

<PASTE THE FULL CONTENTS OF decisions.md HERE AT EMIT TIME>

=== EPHEMERAL DECISIONS THIS PHASE (not in decisions.md) ===

These are scope choices specific to THIS phase. Do not relitigate without a
real reason. Source of truth is the phase brief.

- <phase-specific decision with specifics>
- <phase-specific decision with specifics>

=== RISKS TO WATCH FOR ===

- <concrete risk + fix pattern or file>
- <concrete risk + fix pattern or file>

Report progress as you go. After each task: one short sentence of what
changed + the commit SHA. Ask before deviating from the plan.

Start by <explicit starting instruction — "asking the user X" or "running
verification commands" or "executing Task N">.
```

---

**Why this prompt is shaped the way it is:**

- Names docs by exact path so the agent doesn't search for them.
- Restates starting-state assertions (test count, routes) so the agent can verify state matches expectation.
- **Inlines `rules.md` and `decisions.md` verbatim.** The source of truth is in those files (so there's no drift across phases), but the prompt is still paste-and-go.
- Separates durable (in rules.md/decisions.md) from ephemeral (phase-scoped) so the fresh session knows what's open to negotiation and what isn't.
- Explicitly says "ask before starting" for the smoke-test question and task selection — protects against eager-agent misfires.

**When to use this prompt:**

- Starting a new Claude Code session with this repo as the working directory.
- Context for the current session has gotten too long and the phase is only partially done — update the phase brief, regenerate this prompt (the inlined rules/decisions get refreshed automatically), and paste into a new session.

**Emit-time reminder for the handoff author:**

When you generate this prompt, literally read `rules.md` and `decisions.md` with the Read tool and paste their contents into the marked sections. Don't summarize them — verbatim only. That's what makes the prompt self-contained.
