# Handoff Prompt Template

Paste the fenced block below into a fresh Claude Code session. The agent will read the right docs, verify state, and start executing from the designated starting point.

---

```
I'm resuming <project-name> on the <branch-name> branch.
<One sentence: what's done, what's next, whether this is mid-phase or starting fresh.>

Read these in order before doing anything:

1. <path-to-handoff-brief>
   — session-transfer brief with current state, locked-in decisions,
     and the task list at a glance
2. <path-to-plan-doc>
   — the full implementation plan / task checklist
3. <path-to-prior-handoff-if-any>
   — what the prior phase shipped and the patterns this phase follows
4. <path-to-high-level-plan-if-relevant>
   — overall architecture / multi-phase context

Then check these memories:
- <memory-name>
- <memory-name>
- <memory-name>

Current state: <branch> branch, <summary of what exists>.
`<baseline verification command>` all green (<N tests passing>).
Routes / <URL>, / <URL>, and / <URL> all render.

Start by executing <Task N> from the plan doc — don't re-plan.
The plan is authoritative. Deviations need explicit user approval.

Hard rules (carried forward from prior phases):
- <Rule with brief reason>
- <Rule with brief reason>
- <Rule with brief reason>
- <Rule with brief reason>

Key decisions already locked in (details in the handoff brief —
do NOT relitigate without a real reason):
- <Concrete decision with specifics>
- <Concrete decision with specifics>
- <Concrete decision with specifics>
- <Concrete decision with specifics>

Report progress as you go. After each task: one short sentence of
what changed + the commit SHA. Ask before deviating from the plan.
If you hit <known gotcha from prior phase>, pause and surface the
details — the fix pattern is in <file path>.

Start with <Task N> <specific first step>.
```

---

**Why this prompt is shaped the way it is:**

- It names docs by exact path so the agent doesn't search for them
- It restates starting-state assertions (test count, routes) so the agent can verify the state matches expectation
- It repeats locked-in decisions inline — the brief says them too, but the prompt can't assume the agent will read the brief before acting
- It explicitly says "don't re-plan" — the plan doc is authoritative
- It names the known gotcha from the prior phase and points at the fix pattern so a fresh agent hitting the same thing knows the playbook instead of rediscovering from scratch
- It repeats hard rules (dev servers, forbidden directories, etc.) that a fresh session might otherwise default-break

**When to use this prompt:**

- Starting a new Claude Code session with this repo as the working directory
- Any time this session's context gets too long and the phase is only partially done — in which case, update the handoff brief first to reflect which tasks are completed, then paste this prompt into a new session so it picks up where you left off
