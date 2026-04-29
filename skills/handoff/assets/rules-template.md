# Hard Rules — Cross-Cutting

**Scope: project-wide.** Rules in this file apply regardless of which surface (compass, studio, atlas, etc.) you're working in — safety boundaries, repo-wide conventions, platform/tooling quirks that bite anywhere.

**For surface-specific rules, see `<surface>/rules.md`.** Examples: editor-only invariants, library-only data access patterns, polygraph-only verdict semantics. The handoff skill's classification step decides which file a new rule lands in; the tie-breaker is "when in doubt, promote to cross-cutting" — duplication across surface files is the failure mode.

Single source of truth. Handoff prompts name this file as a mandatory read but never inline it.

Rules are grouped below for readability. Add new rules under the best-fitting section with a one-line reason (the reason is what lets a future agent judge edge cases; rules without reasons get debated or ignored). Use ISO dates as sub-headers when rules are added in response to a specific incident, so readers can see the history.

---

## Safety and destructive operations

<!-- e.g. "Never force-push to main; if you think you need to, pause and ask." -->
<!-- e.g. "Never run migrations against production endpoint X — verify DATABASE_URL first." -->

---

## Commit and PR discipline

<!-- e.g. "Never commit unless the user explicitly asks." -->
<!-- e.g. "Never skip pre-commit hooks (--no-verify) unless the user explicitly requests it." -->

---

## Directories and code boundaries

<!-- e.g. "Do not modify `../polygraph`, `../conpass`, `../realvoices` — read-only reference codebases." -->
<!-- e.g. "UI primitives must come from `@unum/ui` — never hand-roll <button>, <Dialog>, etc." -->

---

## Platform and tooling quirks

<!-- e.g. "drizzle-kit migrate is broken with pgSchema — use `packages/db/scripts/run-migrations.ts`." -->
<!-- e.g. "Neon HTTP driver returns `{ rows: [...] }`, not a plain array — cast `(result as unknown as { rows: T[] }).rows`." -->

---

## Project conventions that would otherwise be default-broken

<!-- e.g. "No `.js` extensions in imports — tsx + tsconfig resolve `.ts` directly." -->
<!-- e.g. "Don't start dev servers — the user runs them." -->

---

## When a rule should be added here

- A correction the user makes once → add immediately with the reason.
- A rule repeated across two or more phase briefs → promote here and delete from the briefs.
- A platform/tooling quirk that bit someone → add with a pointer to the fix pattern.

## When a rule should be removed

- The underlying condition no longer applies (e.g. the tooling quirk was fixed upstream).
- The user explicitly says "ignore this rule from now on."

Never remove a rule silently. Commit the removal with a one-line explanation.
