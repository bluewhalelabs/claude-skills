# Durable Decisions — `<surface>`

**Scope: surface-specific.** Decisions in this file shape the `<surface>` workstream only (e.g. compass library, studio template editor, polygraph claim chain). Decisions that apply regardless of surface live in the top-level `decisions.md` and should not be duplicated here.

**Tie-breaker:** if a decision plausibly governs two or more surfaces, it does not belong here — promote it to top-level. Duplication across surface files is the failure mode the layered model prevents.

Single source of truth for `<surface>` architectural decisions. The handoff prompt names this file as a mandatory read; it is not inlined.

Append-mostly. A decision is revised only when explicitly reversed; keep the old entry with a "Superseded by #N" note so readers can see the evolution.

Every entry needs a one-line **Reason** — without it, a future agent will re-debate the decision from scratch.

---

## Format

```
## N. <Decision title>  _(YYYY-MM-DD)_

<One or two concrete sentences stating the decision. Be specific: names, file
paths, flags, version numbers. Abstract summaries ("we use a hybrid approach")
hide the details the next session actually needs.>

**Reason:** <Why this decision was made. What tradeoff was chosen over what
alternative. Reference a session or PR if useful.>

**Superseded by:** <Decision #M, if applicable. Only add when a later decision
explicitly reverses this one.>
```

---

## Decisions

<!--
Example entries (delete before first real use):

## 1. Editor uses Cartesian coords with y-up  _(2026-04-29)_

`Canvas.tsx` works in y-up Cartesian space; conversions to DOM coords (y-down)
happen only at the render boundary. All overlay math (selection, resize, rotate)
operates in Cartesian.

**Reason:** Aligns with how designers think about layouts; one conversion
boundary instead of scattered sign flips.

## 2. Library reads from `library_items` view  _(2026-04-30)_

`packages/db` exposes a `library_items` view that joins folders + assets with
scope-first ordering already applied. App code reads the view; only the
ingest pipeline writes to the underlying tables.

**Reason:** Centralizes scope ordering so every consumer sees the same shape
without per-caller joins.
-->
