# Kickoff Prompt Template

Paste the fenced block below into a fresh Claude Code session. It is **ruthlessly terse** — hard cap ≤50 lines, aim for 25-35. The fresh agent reads the kickoff brief plus the four durable docs (surface + cross-cutting, rules + decisions) directly from the repo; the prompt just gets it oriented and points at the open questions it must resolve before picking the first task.

**If your prompt is longer than 50 lines, you are inlining content that belongs in the brief.** Open questions, scope statements, and constraints all go in the brief — the prompt names the files and the first thing to ask, not the framing itself.

---

```
I'm kicking off <initiative-name> in `<project-name>` on `<branch-name>`, working in the `<surface>` surface. <One sentence: what we're about to build, why we picked this surface.>

Worktree / repo root: <absolute path>

This is a KICKOFF — nothing has shipped on this initiative yet. The brief captures intent, not state.

READ THESE FIVE FILES BEFORE ANY ACTION — do not skip. Order: most-specific first.

1. <path-to-kickoff-brief>                            (kickoff intent, scope, open questions, first tasks)
2. <path-to-surface-rules.md>                         (surface-specific rules)
3. <path-to-surface-decisions.md>                     (surface-specific decisions)
4. <path-to-top-level-rules.md>                       (cross-cutting rules)
5. <path-to-top-level-decisions.md>                   (cross-cutting decisions)

Starting state:
- Branch <name>, HEAD <SHA>, no work shipped yet on this initiative — fresh start.

Before executing anything, ASK the user the open questions from the brief. The brief lists them under "Open questions the kickoff session did NOT resolve". Top priorities to surface first:
  (a) <highest-priority open question — one line>
  (b) <second open question — one line>
  (c) <third open question — one line, or omit>

Once those are resolved, start with the first task in the brief's "First tasks" table. Report progress after each task: one short sentence + commit SHA. Ask before deviating.
```

---

**Length check before emitting:**

- Count the lines in your prompt. Over 50? Cut.
- Did you list more than 3 open questions? Cut to the 3 the fresh session needs first to start the conversation; the rest stay in the brief.
- Did you list scope, constraints, or task descriptions inline? Move them to the brief — the prompt only needs to point at the brief and tell the agent what to do first.
- Did the opening sentence become a paragraph of background? Collapse it. The brief carries the full context.

**When to use this prompt:**

- Starting a fresh Claude Code session against this repo for a new initiative that has nothing shipped yet.
- The kickoff conversation just produced the brief; the next thing is to hand the brief off to a clean session that will actually do the work.

**Why this prompt is shaped the way it is:**

- Five mandatory reads at the top, ordered most-specific first. Kickoff sessions tend to launch with eager agents who skip context — naming the files explicitly with a mandatory marker beats hoping they'll find them.
- Single starting-state line — there's no shipped state to verify, no test count to assert, no commits to enumerate. The branch + HEAD is enough.
- The "ASK the user" block is the load-bearing surface in a kickoff prompt. Open questions are how the fresh session knows where to start the conversation rather than where to start coding.
- "Start with the first task" is deferred until questions resolve — fresh agents otherwise read the task table and immediately start typing code, before the user has confirmed the open tradeoffs.
- No "ephemeral decisions" or "known gotchas" sections — typically empty for kickoffs and adding them as placeholders just dilutes the prompt.
