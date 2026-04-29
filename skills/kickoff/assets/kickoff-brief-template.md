# <Initiative or Topic> — Kickoff Brief

**Date:** <YYYY-MM-DD>
**Surface:** `<surface>` (e.g. compass, studio, atlas, polygraph, boost, ad-analysis)
**Branch:** <git-branch-name>
**Status:** kickoff — nothing shipped yet

> Durable rules live in two places, by scope:
> — `<handoff-dir>/<surface>/rules.md` — surface-specific rules
> — `<handoff-dir>/rules.md` — cross-cutting rules
> Durable decisions follow the same split (`<surface>/decisions.md` vs top-level `decisions.md`).
> The paste-in prompt names all four files but does **not** inline them. The brief is initiative-scoped.
>
> This is a **kickoff brief** — it captures *what we're about to do*, not *what we did*. The next handoff cycle on this initiative (after work has actually shipped) uses the standard `handoff` capture brief template and fills in shipped-work / decisions / risks / environment sections.

---

## What a fresh session needs to read, in order

1. This doc — kickoff intent, scope, open questions, first tasks
2. `<handoff-dir>/<surface>/rules.md` — surface-specific rules (most relevant first)
3. `<handoff-dir>/<surface>/decisions.md` — surface-specific decisions
4. `<handoff-dir>/rules.md` — cross-cutting rules
5. `<handoff-dir>/decisions.md` — cross-cutting decisions
6. `<path to plan or PR if relevant>` — execution detail (only if a plan was sketched in the kickoff conversation)

---

## Scope

What this initiative IS — concrete boundary statement. Two-to-four sentences.

**Out of scope** (explicit non-goals — what we are NOT doing as part of this initiative, even if related):
- <thing we're explicitly not addressing>
- <thing we're explicitly not addressing>

---

## Why now

The motivating constraint behind kicking this off — incident, deadline, user ask, opportunity, or "the previous approach didn't work because X." Two-to-four sentences. This is what tells a future-you (or a fresh session) whether the initiative is still load-bearing if priorities shift.

---

## Open questions the kickoff session did NOT resolve

The fresh session that picks this up should ask the user about these BEFORE picking the first task. Real tradeoffs the kickoff conversation didn't decide.

1. <question — what's the tradeoff, what would each answer imply>
2. <question>
3. <question>

---

## First tasks

The 2-5 concrete next steps in rough order, as the user described them. Not a full plan — the plan emerges from the first session. This is just what the user said "do first."

| # | Task | Notes |
|---|---|---|
| 1 | <task> | <one-line note> |
| 2 | <task> | <one-line note> |
| 3 | <task> | <one-line note> |

**Cross-surface touchpoints (if any):** <list secondary surfaces this initiative will touch and the area under each, e.g. "studio: brief modal will need to render the new attachment kind"; delete this line if the initiative is single-surface>

---

## Known constraints

Load-bearing "must" / "must not" statements from the kickoff conversation. Not full requirements — just the constraints that limit the design space.

- <constraint with one-line reason>
- <constraint with one-line reason>

---

## Starting baseline

Where we're starting from, NOT what this initiative shipped (the initiative has shipped nothing yet).

- **Branch:** <name>, HEAD is <SHA> (<one-line commit subject>)
- **Working tree:** <clean, or note what's there>
- **Tests:** <baseline pass count if relevant — what's currently passing on this branch>
- **Env vars required for the work:** <names only if the kickoff conversation surfaced specific ones; otherwise omit>

---

## What's NOT in this brief (yet)

These sections fill in on the next handoff cycle (after this initiative has shipped work):
- What already shipped — empty by definition for kickoff
- Ephemeral decisions this initiative — fill in as scope choices get made during the work
- Risks to watch for — fill in as concrete pitfalls emerge during implementation
- Key facts (snapshot) — fill in once there's actual point-in-time state worth recording

If the kickoff session DID make a durable rule or decision (rare — usually kickoffs just frame work, they don't decide architecture), classify it normally and write it into the right `rules.md` / `decisions.md`. The brief itself stays kickoff-shaped.
