---
name: kickoff
description: Use when starting a new initiative from scratch with nothing shipped yet — to scaffold a workstream's brief, prompt, and INDEX entry under the right surface in `docs/handoffs/`. Triggers on future-tense feature/capability pitches like "let's add X", "let's build X", "let's wire up X to <surface>", "let's tackle <feature/bug>", or explicit "/kickoff <pitch>". Does NOT touch git/worktree/DB mechanics — for environment isolation use the `start` skill. Does NOT capture work-in-progress state — for that use the `handoff` skill (which assumes shipped work to operate on). Sits alongside `handoff` in the same layered docs ecosystem (surface dirs under `docs/handoffs/`); writes initiative briefs into the same location, points fresh sessions at the same 5-file mandatory-read block. Use it when a user pitches new work and `INDEX.md` shows no active initiative covering the topic on the relevant surface.
---

# Initiative Kickoff

When a new initiative is starting from scratch — there's no shipped state to capture, just intent to record so a fresh session can pick up the work — `kickoff` produces the doc scaffolding that makes the workstream a first-class entry in the handoff ecosystem.

It writes one paste-in prompt + one initiative brief into the right surface dir under `docs/handoffs/`, and adds the initiative to `INDEX.md`. The session that picks up the prompt then does the actual work; subsequent transitions on this initiative use `handoff` (capture mode), which assumes shipped state to operate on.

## Boundaries

`kickoff` is intentionally narrow:

- **It scaffolds docs, nothing else.** No branches, no worktrees, no DB branches, no code changes.
- **It does not run the `handoff` skill's eight-step process.** Verifying HEAD, classifying durable rules/decisions, and pruning are no-ops on an initiative with no work yet — running them on a kickoff makes the experience ceremonial and burns user attention.
- **It is NOT for resuming an existing initiative.** If `INDEX.md` already shows an active brief covering the topic, the user is *resuming*, not kicking off — defer to whichever path they actually want (read the existing brief, or hand off the current session via `handoff`).
- **It is NOT for environment isolation.** If the user wants a separate worktree / branch / DB for this work, that's the `start` skill's job. `start` and `kickoff` are independent — most new initiatives share an existing worktree with sibling initiatives on adjacent surfaces and only need kickoff. A few warrant both, run sequentially.

## Relationship with the handoff skill

`kickoff` and `handoff` share the same layered docs ecosystem and the same surface directory tree. They differ on what they write:

| | `kickoff` | `handoff` (capture mode) |
|---|---|---|
| Trigger | Future-tense pitch | Past-tense state-transfer signal |
| Status of the initiative | Net-new | Has shipped work |
| Brief template | `assets/kickoff-brief-template.md` | `handoff/assets/handoff-brief-template.md` |
| Prompt template | `assets/kickoff-prompt-template.md` | `handoff/assets/handoff-prompt-template.md` |
| Steps | 4 (surface, brief, prompt, INDEX) | 8 (incl. verify state, classify durable, prune) |
| Touches `<surface>/rules.md` / `decisions.md`? | Only if the kickoff session genuinely produced durable content (rare) | Yes — every cycle |

A typical initiative lifecycle: `kickoff` once at the start → `handoff` (capture) repeatedly through development → final brief deleted or archived when the initiative ships, durable content already promoted along the way.

## When to use this skill

Trigger on any of these:

- Future-tense work pitch: "let's add X", "let's build X", "let's wire up X to <surface>", "let's tackle <bug/feature>", "I want to add X to <surface>"
- Explicit "/kickoff <pitch>"
- A `start` invocation that asks for follow-up doc scaffolding (not automatic; runs only if the user opts in)

**Detection check before running:** read `INDEX.md`. If an active initiative already covers the topic on the relevant surface, the user is resuming, not kicking off — don't run `kickoff`. If no surface fits and the work is one-off (won't recur), the brief can land at the top level instead of creating a surface dir.

When in doubt: ask the user "is this kicking off new work, or are we resuming something in flight?" Cheaper than guessing.

## Process

Four steps. The shared layered ecosystem (top-level + surface durable docs, INDEX, etc.) is exactly what the `handoff` skill describes — see `../handoff/SKILL.md` for the full layout. `kickoff` reads from that ecosystem and writes only the brief/prompt/INDEX pieces.

### 1. Locate the handoff directory and identify the surface

Find `docs/handoffs/` (or whichever location the project uses — check existing convention; default `docs/handoffs/` if first-time setup; ask the user if neither convention exists nor a default fits).

Read `INDEX.md` if present, to see existing surfaces and active initiatives. Then identify the surface this kickoff lands in:

- **New initiative under an existing surface** → use that surface dir. (Most common.)
- **Brand-new surface** → propose the surface name (e.g. "this looks like an `audit/` workstream — confirm?"). Surface dirs are created lazily — make the dir, but **do not** create empty `rules.md` / `decisions.md` placeholders. They'll be created on the first capture-mode handoff that produces durable surface-specific content.
- **Genuine one-off (no surface fits and the work won't recur)** → brief lives at the top level (`docs/handoffs/<date>-<slug>-handoff.md`). Don't invent a single-use surface.

If the repo is on the legacy non-layered handoff layout, **defer to `handoff` migration mode first** (the `kickoff` skill won't write into a half-migrated tree). Run kickoff once migration is complete.

### 2. Write the kickoff brief

Use `assets/kickoff-brief-template.md`. Brief lives at `<handoff-dir>/<surface>/<YYYY-MM-DD>-<slug>-handoff.md` (or top-level for one-offs).

Sections lead with **intent**, not state:

- **Status:** `kickoff — nothing shipped yet`. Branch + HEAD captured as a starting baseline only.
- **Scope** — what this initiative IS (and explicit out-of-scope for what it isn't).
- **Why now** — the motivating constraint behind starting this work.
- **Open questions** — what the fresh session must ask the user before picking the first task.
- **First tasks** — the 2-5 concrete next steps in rough order, as the user described them.
- **Known constraints** — load-bearing "must" / "must not" statements from the kickoff conversation.
- **Cross-surface touchpoints** (if any) — secondary surfaces this initiative will touch.
- **Starting baseline** — branch, HEAD SHA, working-tree state, baseline test count. Captured as the "where we're starting from" snapshot, NOT as "what this initiative shipped."

Sections that capture-mode briefs lead with — "What already shipped," "Ephemeral decisions this initiative," "Risks to watch for," "Environment state" — get one-liners or are omitted entirely. They fill in on the **next** handoff cycle (capture mode) once work has happened.

### 3. Write the paste-in prompt

Use `assets/kickoff-prompt-template.md`. Same shape as the handoff prompt — same ≤50-line cap, same five-file mandatory-read block (initiative brief + surface rules + surface decisions + top-level rules + top-level decisions), same explicit "ASK the user" closing block.

What's different in kickoff prompts:

- **Starting state** is one short bullet: `Branch <name>, HEAD <SHA>, no work shipped yet — fresh start.`
- **Ephemeral decisions** and **Known gotchas** are typically empty for a kickoff — omit those sections entirely if there's nothing to say. The prompt is already minimal; fewer bullets is fine.
- **The "ASK the user" block becomes load-bearing.** It carries the open questions from step 2 — the fresh session uses these to start the conversation, not to start work. List the 2-3 highest-priority questions; longer lists belong in the brief.

### 4. Update `INDEX.md`

Add the new initiative under its surface in the **Active** section. Mark with a status hint: `_kickoff — first tasks pending_` or similar. The next capture-mode handoff on this initiative will move it to a normal active entry as work progresses.

If `INDEX.md` doesn't exist yet (first kickoff in a project new to layered handoffs), create it from scratch by walking the tree — same regeneration logic the handoff skill uses in its step 8.

## Anti-patterns

Things that look right but produce ceremonial or drift-prone kickoffs:

- **Running the full handoff capture process on a kickoff.** Verifying HEAD, classifying durable rules/decisions, and pruning have nothing to operate on. They make the skill feel heavy and waste the user's attention. If the kickoff session DID produce a genuine durable rule (rare — usually kickoffs frame work, they don't decide architecture), classify and write it through normally; the brief itself stays kickoff-shaped either way.
- **Treating kickoff as a planning replacement.** Kickoff captures *what* the user said to start with — first tasks, scope, open questions — not *how* the work will be sequenced or implemented. If the kickoff conversation went deep on a multi-step plan, write that plan to a separate file (`docs/plans/<topic>.md` or wherever the project keeps them) and reference it from the brief; don't bloat the brief with implementation detail that will rot before the work ships.
- **Skipping the open-questions section to "be efficient."** Kickoff briefs without open questions push the fresh session into an "I'll figure it out" stance, which is exactly the failure mode handoffs are designed to prevent. If there genuinely are no open questions, say so explicitly — but verify by re-reading the kickoff conversation; usually there's at least one tradeoff the user implicitly punted on.
- **Re-using a stale kickoff brief by editing it after work starts.** Once the initiative produces shipped work, the next handoff is a capture-mode handoff — the brief evolves into a capture brief (or a new one supersedes it). Don't keep the kickoff brief evergreen by retrofitting "what shipped" sections into it; that loses the kickoff-vs-progress distinction that makes the next session's read order legible.
- **Creating a surface for a one-off.** If the work genuinely won't recur (e.g. a one-week migration script), the brief lives at the top level. Don't manufacture a single-use surface dir.
- **Running kickoff when an active brief already covers the topic.** That's resumption, not kickoff — read the existing brief and continue, or run capture-mode handoff to transition state.

## Templates

Two templates live in `assets/`:

- `assets/kickoff-brief-template.md` — fill-in structure for the kickoff brief.
- `assets/kickoff-prompt-template.md` — fill-in structure for the kickoff paste-in prompt.

Both share design with the corresponding `handoff` skill templates and write into the same surface tree. The split exists because kickoff briefs/prompts have different section emphasis (intent over state) and using the wrong template makes the resulting docs feel ceremonial in the opposite direction.

The shared layered docs (top-level + surface `rules.md` / `decisions.md`) are owned by the `handoff` skill — kickoff reads from them but doesn't normally write to them.
