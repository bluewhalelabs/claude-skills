---
description: Create a handoff brief + paste-in prompt to transfer this session's state to a fresh Claude Code session
argument-hint: [optional topic, e.g. "phase 3" or "auth migration"]
---

Invoke the `handoff` skill to produce a two-file handoff for the current session: a brief doc cataloguing state and decisions, and a paste-in prompt a fresh session can consume directly.

Topic for this handoff: $ARGUMENTS

If `$ARGUMENTS` is empty, infer a topic name from the current session's work — something short and specific like "phase 3", "auth migration", or "pulse port completion". Don't ask the user for the topic; pick a reasonable one and mention it in your first reply so they can correct it if wrong.

Follow the skill's documented process:

1. Find where handoff / plan docs already live in this repo (check `docs/plans/`, `docs/handoffs/`, or wherever prior plans are kept). Match the existing convention.
2. Scan the conversation history for locked-in decisions — places where the user explicitly approved, rejected, or corrected course. Quote them faithfully with reasons.
3. Verify current state before writing facts: run `git status`, `git log --oneline`, and whichever test/build commands the project uses. Only claim "N tests passing" or "build green" after seeing it.
4. Write the brief first (the fuller doc), then derive the paste-in prompt from it. Use the templates in `~/.claude/skills/handoff/assets/` as starting points and delete sections that don't apply.
5. Commit both files with a single commit like `docs: handoff brief and paste-in prompt for <topic>`.
6. **Always paste the fenced prompt block inline in your final reply**, in addition to telling the user where the files live. The user wants the prompt accessible directly in the terminal every time — don't wait for them to ask and don't make them open the file. Put the paste-in prompt first in your reply, then a one-line note with the file paths and commit SHA.
