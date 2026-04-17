# claude-skills

A collection of [Claude Code](https://claude.com/claude-code) skills and slash commands maintained by Blue Whale Labs.

## Skills

### `handoff`

Transfer a long-running Claude Code session's state to a fresh session without losing durable rules, architectural decisions, or phase-specific context — and without drifting across copies as the project evolves.

Maintains a **self-contained handoff ecosystem** in one directory under the project (e.g. `docs/handoffs/`). Four artifacts, two durable and two ephemeral:

- **`rules.md`** — durable hard rules (destructive-op guards, platform quirks, forbidden directories, conventions). Single source of truth. Edited in place. Outlives every phase.
- **`decisions.md`** — durable architectural decisions with reasons. Append-mostly. Outlives every phase.
- **`<date>-<topic>-handoff.md`** — per-phase brief. Current state, phase-ephemeral decisions, risks, open questions. Deleted or superseded when the phase's PR merges.
- **`<date>-<topic>-handoff-prompt.md`** — terse paste-in prompt (~60–100 lines). Names the three must-read files at the top; restates only phase-ephemeral decisions and gotchas inline. Does **not** inline the durable docs — the fresh agent reads them directly as step 1, which keeps paste size small and prevents stale copies.

The skill classifies decisions on every run: durable ones land in `rules.md` / `decisions.md` (edited in place), phase-ephemeral ones stay in the brief. Prior phase briefs get superseded or deleted at commit time so the directory doesn't accumulate cruft.

Triggers on phrases like "create a handoff", "context is getting long", or "give me a prompt for the next session". Can also be invoked via the `/handoff` slash command.

See [`skills/handoff/SKILL.md`](skills/handoff/SKILL.md) for the full process and anti-patterns.

## Install

Clone this repo and symlink (or copy) the pieces into your Claude config directory:

```sh
git clone https://github.com/bluewhalelabs/claude-skills.git
cd claude-skills

# Skills
mkdir -p ~/.claude/skills
ln -s "$PWD/skills/handoff" ~/.claude/skills/handoff

# Slash commands
mkdir -p ~/.claude/commands
ln -s "$PWD/commands/handoff.md" ~/.claude/commands/handoff.md
```

Restart Claude Code and the `handoff` skill + `/handoff` command will be available.

## Layout

```
claude-skills/
├── skills/
│   └── handoff/
│       ├── SKILL.md
│       └── assets/
│           ├── rules-template.md              # starter for a project's rules.md
│           ├── decisions-template.md          # starter for a project's decisions.md
│           ├── handoff-brief-template.md      # per-phase brief
│           └── handoff-prompt-template.md     # terse paste-in prompt
└── commands/
    └── handoff.md
```

## Contributing

New skills go under `skills/<name>/SKILL.md`. Slash commands that wrap a skill go under `commands/<name>.md`. Keep each skill's assets (templates, references) inside the skill's own directory.
