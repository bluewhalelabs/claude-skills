# Durable Decisions — Cross-Cutting

**Scope: project-wide.** Decisions in this file shape the project regardless of which surface (compass, studio, atlas, etc.) you're working in — data layer choices, repo-wide conventions, cross-cutting architectural calls.

**For surface-specific decisions, see `<surface>/decisions.md`.** Examples: editor coordinate system, library federation read path, ad-pipeline scoring model. The handoff skill's classification step decides which file a new decision lands in; the tie-breaker is "when in doubt, promote to cross-cutting" — duplication across surface files is the failure mode.

Single source of truth. Handoff prompts name this file as a mandatory read but never inline it.

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

## 1. Data layer is polymorphic label_assignments, not per-entity tables  _(2026-04-16)_

`core.label_assignments` with `entity_type ∈ {content, account, ...}` stores
every label attachment. Unique on `(collection, label, entity_type, entity_id)`.
New labelable entity types are additive — no schema change per type.

**Reason:** Same service + UI primitives for every labelable entity; avoids N
per-target tables as the system grows.

## 2. Predicate JSON is versioned; predicates themselves aren't  _(2026-04-16)_

`label_rules.predicate_version = 1`; the evaluator dispatches on version so v1
and a future v2 can coexist. Label definitions and assignments reference labels
by immutable ID, so rename is free. Context/field registries are treated like
API contracts — add freely, deprecate softly, never rename.

**Reason:** The only layer where JSON semantics can legitimately shift is the
predicate; everything else is stable.
-->
