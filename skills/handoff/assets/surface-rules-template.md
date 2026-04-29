# Hard Rules — `<surface>`

**Scope: surface-specific.** Rules in this file apply only when working in the `<surface>` workstream (e.g. compass library, studio template editor, polygraph claim chain). Rules that apply regardless of surface live in the top-level `rules.md` and should not be duplicated here.

**Tie-breaker:** if a rule plausibly applies to two or more surfaces, it does not belong here — promote it to top-level. Duplication across surface files is the failure mode the layered model prevents.

Single source of truth for `<surface>` rules. The handoff prompt names this file as a mandatory read; it is not inlined.

Rules are grouped below for readability. Add new rules under the best-fitting section with a one-line reason (the reason is what lets a future agent judge edge cases). Use ISO dates as sub-headers when rules are added in response to a specific incident.

---

## Data access and code boundaries

<!-- e.g. "Library reads from `library_items` view, not raw `compass.folders` join — view enforces scope-first ordering." -->
<!-- e.g. "Editor coordinates are Cartesian with y-up; DOM math conversions live only in `Canvas.tsx`." -->

---

## Conventions specific to this surface

<!-- e.g. "Library sidebar tree uses `folder-sidebar.tsx` — never hand-roll a parallel tree." -->
<!-- e.g. "Template editor toolbar respects mode pill — palette items hide when not in design mode." -->

---

## Surface-specific tooling and platform quirks

<!-- e.g. "Studio API route handles only owner-org authorization; team-level checks happen in the service." -->
<!-- e.g. "Compass library queries must be sorted server-side — client-side sort breaks pagination." -->

---

## When a rule should be added here

- A correction the user makes that would not apply if you were working in a different surface.
- A rule repeated across two phase briefs within this surface → promote here and delete from the briefs.
- A surface-specific tooling quirk that bit someone → add with a pointer to the fix pattern.

## When a rule should be removed

- The underlying condition no longer applies (refactor, surface retired, tooling fixed).
- The rule turned out to apply more broadly than this surface → promote to top-level `rules.md` and delete from here.

Never remove a rule silently. Commit the removal with a one-line explanation.
