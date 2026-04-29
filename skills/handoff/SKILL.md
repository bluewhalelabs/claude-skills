---
name: handoff
description: Use when a long-running work session needs to transfer state to a fresh session. Triggers on phrases like "create a handoff", "prepare a handoff", "hand this off", "context is getting long", "wrap this up so we can continue in a new session", "give me a prompt for the next session", or any time the user signals they want to pause mid-flight on a multi-phase project and resume later. Maintains a layered, per-workstream handoff ecosystem in the project — top-level cross-cutting `rules.md` / `decisions.md`, plus per-surface `<surface>/rules.md` / `<surface>/decisions.md` and per-initiative briefs/prompts inside each surface dir. The directory tree itself is the workstream registry; `INDEX.md` is a regenerated readable view of it. Use proactively when context is filling up mid-multi-phase work — don't wait for an explicit ask. For **starting** a new initiative from scratch (no shipped state to capture), use the separate `kickoff` skill instead.
---

# Session Handoff

When a work session needs to transfer state to a fresh session, produce a handoff into a **layered ecosystem** that lives entirely under one directory. Durable content is split by **scope** (cross-cutting vs surface-specific). Ephemeral content is per **initiative** and lives under its surface dir. Multiple concurrent workstreams coexist without polluting each other's context.

## The five artifact types

The ecosystem has five artifact types, classified along two axes — durable vs ephemeral, and cross-cutting vs surface-specific:

| | Cross-cutting | Surface-specific |
|---|---|---|
| **Durable** | top-level `rules.md` / `decisions.md` | `<surface>/rules.md` / `<surface>/decisions.md` |
| **Ephemeral** | (none — initiatives belong to a surface) | `<surface>/<date>-<slug>-handoff.md` + `-handoff-prompt.md` |

Plus one generated artifact: `INDEX.md` — a readable view of which surfaces and initiatives are in flight, regenerated from the directory tree.

### Why layered

Three problems have to be solved simultaneously:

1. **Durable content gets repeated across handoffs and drifts across copies.** Duplication is the failure mode. Single source of truth fixes this — but only if the source is small enough that fresh agents will actually read it.
2. **Phase-specific state decays fast.** Current HEAD, what shipped this week, who's doing what — obsolete in days.
3. **A multi-workstream project has rules from many surfaces competing for attention.** A monolithic `rules.md` for compass + studio + atlas + polygraph + boost grows past the size where fresh agents will read it. Surface-specific noise pollutes unrelated work.

The layered model adds the **scope axis** to the existing durable/ephemeral split. Surface-specific durable content stays close to its initiatives. Cross-cutting durable content stays in one shared place. Each individual file is small enough to be read in full by a fresh agent.

## Directory layout

```
docs/handoffs/
  INDEX.md                       # generated view of active surfaces + initiatives
  rules.md                       # cross-cutting durable rules
  decisions.md                   # cross-cutting durable decisions
  <surface>/                     # one dir per active workstream surface
    rules.md                     # surface-specific durable rules
    decisions.md                 # surface-specific durable decisions
    <YYYY-MM-DD>-<initiative>-handoff.md
    <YYYY-MM-DD>-<initiative>-handoff-prompt.md
  _archive/                      # optional — shipped initiatives kept readable
    <surface>/<initiative>.md
```

The handoff ecosystem is portable: same layout works in any repo. The skill never scatters content into `CLAUDE.md`, memory files, or project-specific conventions — those vary by project.

### What is a surface

A **surface** is a top-level product area users interact with. Examples in a multi-product monorepo: `compass/`, `studio/`, `atlas/`, `polygraph/`, `boost/`, plus non-route workstreams that are coherent product areas (`ad-analysis/`).

**Packages are not surfaces.** Their durable rules and decisions live with whichever surface drives the work, under a topic heading inside that surface's `rules.md`. (Example: if only studio drives the media-template editor today, durable rules about the editor live in `studio/rules.md` under `## Media Template Editor`.) If a package later gets used by multiple surfaces and develops genuinely cross-cutting rules, promote those to top-level `rules.md`.

### Lazy creation

- A surface dir is created on the first handoff for that surface. Empty top-level dir for an unused surface = waste.
- Surface `rules.md` / `decisions.md` are created the first time a *durable surface-specific* item is classified to that surface. Don't create empty placeholders.
- One-off work that won't recur (e.g. a one-week migration script): brief lives at the top level; no surface dir needed.

### Multiple concurrent initiatives per surface

Allowed. Each initiative gets its own `<date>-<slug>-handoff.md` + matching `-handoff-prompt.md` pair inside the surface dir. They share the surface's durable docs.

### Cross-surface initiatives

If an initiative spans surfaces (e.g. library federation touching compass + studio + storage), the brief lives under the **primary** surface and the brief calls out cross-surface touchpoints in its execution path. Don't create one brief per surface for a single initiative — that's how state drifts.

## The two-axis classification (the core skill change)

For every rule or decision the session produces, classify along **two axes**:

1. **Durable or ephemeral?**
   - Durable = true across phases, will outlive this initiative.
   - Ephemeral = specific to this initiative's scope, doesn't survive past it.
2. **If durable: cross-cutting or surface-specific?**
   - **Cross-cutting** = applies regardless of which surface you're working in. Examples: "never DDL on main", "kebab-case files", "use shared `@unum/ui` components", "never commit unless asked".
   - **Surface-specific** = only matters when working in that surface. Examples: "media-template editor uses Cartesian coords with y-up", "compass library reads from `library_items` view, not raw tables", "polygraph claim chain skips for own-org content".

**Tie-breaker:** when a rule plausibly applies to two or more surfaces, it goes cross-cutting. Duplication across surface files is the failure mode the layered model is trying to prevent — when in doubt, promote to cross-cutting.

Capture the *reason* for every durable item. A rule or decision without a reason gets re-debated.

## The context-capture philosophy

The markdown files are where context accumulates. The **prompt is just an index card** pointing at them. When you're tempted to add "one more thing" to the prompt — a commit summary, a gotcha, a task detail — the right move is almost always to put it in the brief (or promote it to the right durable doc) and leave the prompt alone.

A handoff's quality is measured by three things:

1. **Is the detail captured somewhere durable?** (It should be — in the brief, surface durable docs, or cross-cutting durable docs.)
2. **Is each individual durable file small enough to actually be read by a fresh agent?** (Target: <10KB per file. If a surface's `rules.md` exceeds this, decompose by topic *within* the file via headings before considering further structure.)
3. **Is the prompt short enough that pasting it doesn't burn context?** (Target: ≤50 lines.)

All three can be true at the same time only if the layering is doing its job — surface dirs absorb surface-specific noise, cross-cutting docs stay narrow, the prompt stays terse.

## Size must go down over time

The ecosystem is lossy by design. Every handoff cycle, some content leaves the system:

- Tasks that landed disappear from the task queue.
- Gotchas that got fixed disappear from the gotchas list.
- Open questions that got answered either disappear (if the answer was scoped to the initiative) or promote to the appropriate `decisions.md` (if durable).
- Ephemeral decisions that were superseded get deleted.

**If a brief is bigger than its predecessor in the same surface, something is wrong.** Either a genuinely larger initiative is in flight (expected) or old content is accumulating (bad — prune it). Durable docs can grow, but slowly, and only with net-new durable content — not by accumulating ephemera that was never promoted properly.

The pruning step (step 7) is where this discipline is enforced. Do not skip it.

## When to use this skill

Trigger on any of these:

- Explicit ask: "create a handoff", "prepare a handoff", "hand this off", "give me a prompt for the next session"
- Context pressure: "context is getting long", "let's wrap up so we can continue later"
- Mid-initiative pause: one initiative done, pausing before the next

Use it proactively if you notice the session is clearly approaching context limits mid-multi-phase work. Don't wait for an explicit ask if the runway is about to run out.

**Don't use this skill to scaffold a new initiative from scratch.** When a user pitches new work ("let's add X", "let's build X") and there's nothing shipped yet, the `kickoff` skill handles that — it produces an initiative brief + prompt + INDEX entry without running this skill's state-capture, decision-classification, or pruning steps (which all assume shipped work to operate on). The `handoff` skill comes back into play once the initiative has actually produced work and a session needs to transfer that state forward.

**Migration mode** is a one-time path inside this skill, triggered when the repo is detected on the legacy non-layered layout. See "Migration mode" below.

## Process

Eight steps. Don't skip the "verify current state" step or the "classify durable vs ephemeral, cross-cutting vs surface-specific" step — those are what keep the ecosystem working over time. **Migration mode** is its own one-time process (see "Migration mode" below).

### 1. Locate (or initialize) the handoff directory and identify the surface

Look for an existing convention. If you've written a handoff in this repo before, it's already there — typically:

- `docs/handoffs/`
- `docs/plans/` (older convention, may mix with planning docs)
- `docs/superpowers/plans/` (seen in some repos)

List the docs dir and look for prior `*-handoff*.md` files. Match the existing location.

If this is the **first** handoff in the project, ask the user where they want the directory. Default suggestion: `docs/handoffs/`. Don't invent a location silently.

**Identify the surface for this handoff.** If the user said one ("handoff for compass library"), use it. If not, ask: "Which surface does this initiative belong to (e.g. compass, studio, atlas)?" New initiatives need a surface before any files get written. The exception is a genuine one-off task that doesn't fit any surface — that brief can live at the top level.

**Detect legacy layout.** If you find existing top-level `rules.md` / `decisions.md` plus briefs sitting at the top level instead of in surface dirs, this repo is on the pre-layered layout. Offer a one-time migration (see "Migration mode" below) before continuing. Don't write new layered handoffs alongside the legacy structure — that creates two parallel layouts.

**Check for durable docs:**

- **Top-level `rules.md` / `decisions.md` exist** → read them.
- **Top-level missing** → first-time setup. Ask the user if you should initialize from `assets/rules-template.md` and `assets/decisions-template.md`. If yes, draft initial cross-cutting content from this session and get user approval before saving.
- **`<surface>/rules.md` / `<surface>/decisions.md` exist** → read them.
- **Surface durable docs missing** → don't auto-create. Step 4 will create them lazily *if and only if* the session produced surface-specific durable content. From `assets/surface-rules-template.md` and `assets/surface-decisions-template.md`.

### 2. Classify decisions and rules along both axes

Scan the current session for every place the user:

- Approved something ("yes, go with A")
- Rejected something ("no, don't do X")
- Corrected course ("actually, let me clarify…")
- Picked a side when a tradeoff was discussed

For each decision or rule that surfaced, classify it:

1. **Durable or ephemeral?**
2. **If durable: cross-cutting or surface-specific?** Apply the tie-breaker — when plausibly applies to multiple surfaces, promote to cross-cutting.

Write the classification down before moving to step 3 (your future self will thank you when you reach step 4 and need to know which file each item lands in).

Capture the *reason* for every durable item.

### 3. Verify current state

Before writing facts into the brief, confirm they're true *right now*:

- Branch: `git branch --show-current`
- Working tree: `git status --short`
- Recent commits: `git log --oneline -20` or scoped to the current initiative
- Tests (if applicable): run the suite, record the pass count
- Build (if applicable): run it, confirm green
- Routes/features: verify with whatever verification the project uses

Don't write "19 tests passing" unless you just saw 19 tests pass. Don't write "routes working" unless the build was green at this HEAD.

### 4. Update the right `rules.md` / `decisions.md` files

Apply the classifications from step 2:

- **Cross-cutting durable** → top-level `rules.md` / `decisions.md`.
- **Surface-specific durable** → `<surface>/rules.md` / `<surface>/decisions.md`. Create the surface dir and durable docs lazily here if they don't exist (use `assets/surface-rules-template.md` and `assets/surface-decisions-template.md`).

Use ISO dates (`## 2026-04-29` or similar) so readers can see when a rule was introduced. Don't rewrite history — append new entries at the bottom (or in a dated section).

This step happens **before** the brief is written, so the brief doesn't duplicate durable content.

### 5. Write the initiative brief

Use `assets/handoff-brief-template.md`. The brief lives in the surface dir as `<surface>/<YYYY-MM-DD>-<slug>-handoff.md` (or at the top level for genuine one-off tasks).

Key sections:

- **What to read in order** — the brief itself, then surface `rules.md` + `decisions.md`, then top-level `rules.md` + `decisions.md`, then the PR or plan doc. Don't re-list the durable content; the durable docs are concise enough to read directly.
- **What already shipped this initiative** — specific: commit SHAs, route paths, test counts, verification status.
- **Ephemeral decisions this initiative** — initiative-scoped tradeoffs only.
- **Cross-surface touchpoints** — if the initiative touches surfaces other than the primary one, list each secondary surface and the file/route under it.
- **Key facts (snapshot)** — point-in-time DB state, ids, counts. Note these decay.
- **Execution path at a glance** — remaining tasks or phases, one line each. Point at the plan doc for details.
- **Risks to watch for** — concrete pitfalls with the file or task where each lives.
- **Open questions the fresh session should NOT decide unilaterally** — real tradeoffs for the user to weigh.
- **Environment state** — Git HEAD, test count, routes, env var *names* (never values).

**Do NOT re-list durable rules or decisions in the brief.** The brief points at the four durable docs (surface + top-level pair × 2); the prompt tells the fresh agent to read them.

### 6. Write the paste-in prompt

Use `assets/handoff-prompt-template.md`. The prompt is a single fenced code block followed by a short explanation of when to use it.

**Hard cap ≤50 lines, aim for 30-40.** The prompt's job is to orient a fresh agent and point at the five files — nothing more. Detail belongs in the brief. If the prompt is creeping past 50 lines, you're inlining content that belongs elsewhere; move it out.

The prompt does NOT inline any durable doc.

**Prompt structure (lean version):**

1. **One sentence** of context: project, branch, surface, where we are. Not a paragraph. Do not enumerate shipped commits here — "Phase 2 has ~19 commits on top" is enough; the brief has the list.
2. **Mandatory-read block** (explicit, bold): "READ these five files before any action" — brief, surface `rules.md`, surface `decisions.md`, top-level `rules.md`, top-level `decisions.md`. Just paths, no per-file summaries. Read order: most-specific first.
3. **Starting state** — 3-5 bullets max: HEAD SHA, build/typecheck status, last smoke-test status. External ids only if the fresh session cannot find them via a single command.
4. **Ephemeral decisions — 3 bullets max.** Only the handful the agent needs before picking the first task. Longer lists go in the brief.
5. **Known gotchas — 3 bullets max**, each one line with a file path. Longer lists go in the brief.
6. **Explicit starting instruction**: "Start by asking the user X" or "Start with Task N." Reference the brief for the task queue — **do not re-enumerate tasks in the prompt**.

**What the prompt does NOT contain:**

- The remaining task list with descriptions. (Brief has the table.)
- A second "optional files to read" block beyond the five mandatory.
- Memory-file bullets more than 2-3 entries.
- Any summary of what shipped this initiative.
- Any hard rule or architectural decision. (Those live in the four durable docs.)

The prompt is self-contained for *starting*: it tells the agent WHICH files to read and WHAT to do first. Everything else is one `Read` call away.

### 7. Prune before committing

The ecosystem works only if the files shrink as items move out of scope. Every handoff is also a cleanup pass. Do the pruning **before** you commit.

**Initiative brief (the one you just wrote or updated):**
- Tasks that have landed → delete from the execution path. The brief describes what's next, not what's done. (Shipped work goes in "What already shipped" as a one-liner with SHA; it gets culled on the *next* handoff.)
- Open questions answered → delete, and if the answer is durable, promote to the right `decisions.md`.
- Gotchas fixed in code → delete. If the fix needs to stay top-of-mind, promote to the right `rules.md`.
- Ephemeral decisions superseded → delete.
- "Key facts (snapshot)" that have changed → refresh or delete.

**`<surface>/rules.md` and `<surface>/decisions.md`:**
- Rule no longer applies → delete.
- Two rules say the same thing → merge.
- Rule turned out to apply more broadly than this surface → **promote to top-level** `rules.md`, delete from surface.
- Decision reversed → delete the old entry and write the reversal with reason. No strikethrough archaeology.

**Top-level `rules.md` and `decisions.md`:**
- Same pruning discipline. Plus: rule that turned out to be surface-specific → demote to `<surface>/rules.md`, delete from top-level.

**Prior phase's brief and prompt (in the same surface dir):**
- Prior phase PR merged → delete the prior `*-handoff.md` and `*-handoff-prompt.md`. Git log preserves history. Optionally move the brief to `_archive/<surface>/` if the user wants it kept readable.
- Prior phase still in flight (not merged) → add a one-line `**Superseded by:** <new-brief-path>` banner at the top of the prior brief. The prior brief stays readable but clearly marked.

Never delete `rules.md` or `decisions.md` (top-level or surface). They're durable. But do keep them lean — a 10KB-plus surface rules file gets skipped by fresh agents.

**Size check:** if any of the durable files has grown compared to the prior handoff, justify it. Net-new durable rules or decisions are fine; accumulated ephemera is not.

### 8. Regenerate `INDEX.md`

`INDEX.md` is a readable view of the directory tree. The tree is the source of truth — `INDEX.md` mirrors it. Never hand-edit `INDEX.md` as the source.

To regenerate:

1. List surface dirs under `docs/handoffs/` (excluding `_archive/`).
2. For each surface, list `*-handoff.md` files. Inspect each for `**Status:**` markers (e.g. `paused (<reason>)`, `superseded by <path>`).
3. Group surfaces by status: **Active** (any non-paused initiatives), **Paused** (only paused/superseded initiatives), **Cross-cutting docs** (the top-level pair).
4. Surfaces with no active brief but with surface durable docs → list under Active with "(no active brief — durable docs only)".

Shape (illustrative):

```markdown
# Handoffs Index

_Last updated: 2026-04-29_

## Active

### compass
- 2026-04-30 — library-slice-2 — _phase brief: 2026-04-30-library-slice-2-handoff.md_

### studio
- 2026-04-29 — editor-ux-polish
- 2026-04-30 — media-template-projects

### ad-analysis
- (no active brief — durable docs only)

## Paused

### polygraph
- 2026-03-15 — production-readiness — _superseded by PR #78 review_

## Cross-cutting docs
- rules.md
- decisions.md
```

Commit all affected files (durable edits + pruned entries + new brief + new prompt + prior-brief deletion/supersession + regenerated `INDEX.md`) in a single commit: `docs: handoff for <surface>/<initiative>` (note any significant prunes in the body).

**Always paste the fenced prompt block into your final reply**, in addition to telling the user where the files live. Standing user preference. Put the prompt first (easy to copy from scrollback), then a short note with the file paths and commit SHA.

## Migration mode

The skill detects legacy layout (top-level `rules.md` / `decisions.md` plus briefs sitting at the top level instead of in surface dirs) and offers a **one-time migration** before continuing with a normal handoff.

Migration is gated behind explicit user confirmation — for repos with established top-level docs, the diff is chunky.

Migration steps:

1. **List existing briefs** in `docs/handoffs/`. Ask the user which surface each maps to. (Brief filenames usually telegraph the surface — `editor-ux-polish` → studio, `library-slice-2` → compass — but always confirm.)
2. **Read existing top-level `rules.md` / `decisions.md`.** Walk through each rule/decision and propose a tag: `cross-cutting` or `<surface>`. Show the user the proposed split in one pass; let them correct any misclassifications. Apply the tie-breaker: when in doubt, propose cross-cutting.
3. **Write the new layout:**
   - Top-level `rules.md` + `decisions.md` keep only cross-cutting items.
   - Surface dirs get created with their split content (using `assets/surface-rules-template.md` / `assets/surface-decisions-template.md` headers, then the migrated entries).
   - Existing briefs and prompts move into their surface dirs (no content changes — just path moves; the path references inside them still need updating to point at the new durable doc locations).
4. **Update the briefs and prompts** that just moved: their "What to read in order" sections (and prompt mandatory-read blocks) need to point at the new paths — surface-level + top-level pair × 2.
5. **Write `INDEX.md`** from the new tree.
6. **Commit** as `docs(handoff): migrate to layered workstream layout`.

After migration, proceed with the normal handoff process for whatever initiative triggered the skill.

## Anti-patterns

Things that look right but produce weak or drift-prone handoffs:

- **Duplicating cross-cutting rules into surface `rules.md`.** The whole point of the layer is that cross-cutting lives in one place. If the same rule appears in two surface files, stop and promote to top-level. Same applies to decisions.
- **Duplicating durable rules in every initiative brief.** If the same rule is in this brief AND the prior one AND `rules.md`, you'll have three versions that drift. Keep it in `rules.md` only; the prompt names the file, it doesn't inline the content.
- **Re-enumerating the task queue in the prompt.** The brief has it. The prompt's job is to get the agent to *read* the brief, not to recreate it.
- **Expanding the one-sentence context into a paragraph of shipped work.** "Phase 2 has stacked ~19 commits since <date>" is enough.
- **Fragmenting one initiative across multiple surface briefs.** Cross-surface initiatives have ONE brief in the primary surface; secondary surfaces are listed in the cross-surface touchpoints section. Don't spawn one brief per touched surface.
- **Letting surface `rules.md` grow to monolith size.** If a surface's rules file passes ~10KB, sub-decompose by topic *within* the file (headings) before considering further structure. Don't invent sub-surface dirs.
- **Creating a surface dir for a one-off task.** Brief at the top level is fine for genuine one-offs. Don't create empty `rules.md` placeholders for surfaces with no durable content yet.
- **Treating `INDEX.md` as a parallel list.** Regenerated from the tree at step 8. Never hand-edit as the source.
- **Inventing project-specific locations for rules.** Don't put hard rules in `CLAUDE.md`, `AGENTS.md`, memory files, `docs/engineering/standards.md`, etc. The handoff ecosystem is self-contained.
- **Treating the initiative brief as an archive.** It's ephemeral. Delete or `_archive/` it when the initiative lands. Durable content lives in `rules.md` / `decisions.md`.
- **Vague summaries.** "Made good progress on the auth layer" is useless. Say exactly what shipped: SHAs, route paths, test counts, files added.
- **Decisions without reasons.** Every entry in any `decisions.md` needs a one-line reason. Otherwise the fresh session re-debates it.
- **Skipping the classify step.** If you don't separate durable from ephemeral *and* cross-cutting from surface-specific, everything ends up in the brief and the durable docs get ignored. The whole ecosystem depends on the discipline.
- **"Read the brief for details"** in the prompt. If it matters for starting, inline it in the prompt. The brief is for depth, not essentials.
- **Skipping the "what not to decide" section.** Fresh sessions will eagerly "fix" open questions if not told to ask first.
- **Not verifying current state.** Don't write "tests passing" without running them.
- **Over-abstracting decisions.** "We chose a hybrid approach" hides the specifics. Write concretely with names, paths, flags.

## Edge cases

- **Initiative spans surfaces** → primary surface owns the brief; brief explicitly lists secondary surfaces touched in the cross-surface touchpoints section.
- **Cross-cutting rule discovered while working in a surface** → write it directly to top-level `rules.md`, not the surface file (per tie-breaker).
- **Surface gets retired** → archive the whole surface dir under `_archive/<surface>/`. INDEX stops listing it.
- **Two concurrent initiatives in one surface** → both briefs coexist in the surface dir; each gets its own prompt; INDEX shows both; they share the surface's durable docs.
- **Initiative paused, not shipped** → brief stays in the surface dir with `**Status:** paused (<reason>)` marker at the top; INDEX moves it to a "Paused" section. No deletion until either resumed or formally killed.
- **Surface durable doc grew past ~10KB** → sub-decompose by topic with headings inside the file (e.g. `## Editor`, `## Toolbar`, `## Keyboard`). Don't create sub-surface directories.
- **One-off task that doesn't fit any surface** → brief at the top level is allowed. No surface dir needed.

## Templates

Six templates live in `assets/`:

- `assets/rules-template.md` — starter for the top-level (cross-cutting) rules file.
- `assets/decisions-template.md` — starter for the top-level (cross-cutting) decisions file.
- `assets/surface-rules-template.md` — starter for a `<surface>/rules.md`.
- `assets/surface-decisions-template.md` — starter for a `<surface>/decisions.md`.
- `assets/handoff-brief-template.md` — fill-in structure for each initiative's brief.
- `assets/handoff-prompt-template.md` — fill-in structure for each initiative's paste-in prompt.

The `kickoff` skill (separate) shares this layered ecosystem — it reads the same surface tree, points fresh sessions at the same 5-file mandatory-read block, and writes its initiative briefs into the same `<surface>/<date>-<slug>-handoff.md` location. It just bypasses the state-capture / decision-classification / pruning steps that don't apply when nothing has shipped yet.

Adapt section-by-section to the specific project. Delete sections that don't apply rather than filling them with placeholders.
