---
description: Create a handoff brief + paste-in prompt to transfer this session's state to a fresh Claude Code session
argument-hint: [optional topic, e.g. "phase 3" or "auth migration"]
---

Invoke the `handoff` skill to produce a handoff for the current session. The skill maintains a four-artifact ecosystem in one directory: two durable docs (`rules.md`, `decisions.md`) that outlive phases, plus a per-phase brief and a paste-in prompt. The prompt is ruthlessly terse (≤50 lines) — detail accumulates in the markdown files, and every handoff cycle prunes resolved items.

Topic for this handoff: $ARGUMENTS

If `$ARGUMENTS` is empty, infer a topic name from the current session's work — something short and specific like "phase 3", "auth migration", or "pulse port completion". Don't ask the user for the topic; pick a reasonable one and mention it in your first reply so they can correct it if wrong.

Follow the skill's documented process:

1. Find where handoff / plan docs already live in this repo (check `docs/plans/`, `docs/handoffs/`, or wherever prior plans are kept). Match the existing convention.
2. Scan the conversation history for locked-in decisions — places where the user explicitly approved, rejected, or corrected course. Quote them faithfully with reasons.
3. Verify current state before writing facts: run `git status`, `git log --oneline`, and whichever test/build commands the project uses. Only claim "N tests passing" or "build green" after seeing it.
4. Write the brief first (the fuller doc), then derive the paste-in prompt from it. Use the templates in `~/.claude/skills/handoff/assets/` as starting points and delete sections that don't apply. Keep the prompt to ≤50 lines — do not re-enumerate the task queue, shipped commits, or durable content. The brief carries all of that.
5. **Prune.** Before committing, remove landed tasks, resolved gotchas, answered open questions, and superseded decisions from the brief. Promote anything durable into `rules.md` / `decisions.md`. Delete prior merged phase briefs. The ecosystem's size should go down over time, not up.
6. Commit affected files in one commit like `docs: handoff for <topic>` and note significant prunes in the body.
7. **Always paste the fenced prompt block inline in your final reply**, in addition to telling the user where the files live. The user wants the prompt accessible directly in the terminal every time — don't wait for them to ask and don't make them open the file. Put the paste-in prompt first in your reply, then a one-line note with the file paths and commit SHA.
