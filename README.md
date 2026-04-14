# claude-skills

A collection of [Claude Code](https://claude.com/claude-code) skills and slash commands maintained by Blue Whale Labs.

## Skills

### `handoff`

Transfer a long-running Claude Code session's state to a fresh session without losing locked-in decisions, hard rules, or context.

Produces two artifacts:

- A **brief doc** cataloguing current state, decisions with reasons, risks, and open questions
- A **paste-in prompt** the user drops into a new session — self-contained, no chained reads required

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
│           ├── handoff-brief-template.md
│           └── handoff-prompt-template.md
└── commands/
    └── handoff.md
```

## Contributing

New skills go under `skills/<name>/SKILL.md`. Slash commands that wrap a skill go under `commands/<name>.md`. Keep each skill's assets (templates, references) inside the skill's own directory.
