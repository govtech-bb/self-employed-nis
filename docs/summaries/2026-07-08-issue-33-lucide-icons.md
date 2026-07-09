# Issue #33: Replace the wizard's hand-drawn inline SVG icons with Lucide

**Date:** 2026-07-08
**Branch:** `feat/issue-33-lucide-icons` (based on `dev`)

## Why

`check.html` rendered 39 hand-authored inline SVG icons (the `ICONS` map). Several
domain icons — `maternity`, `paternity`, `invalidity`, `survivors`, `sickness`,
`agri`, `beauty`, `creative` — were ambiguous at rendered size on mobile (28px cards),
while several utility icons were already near-verbatim Lucide copies. This is the exact
defect the predecessor gig-worker prototype was dinged for (its issue #24). Two entries
(`taxi`, `heart`) were dead weight — `taxi` folded into `delivery`, `heart` unused. The
icons also carried no accessibility markup.

## What changed

The `ICONS` map (`check.html`) was rewritten to source every glyph from **Lucide (ISC)**
— one established set. Because all 48 call-sites read from this single map, editing the
map updated every usage; no template or call-site changed.

- 37 used icons swapped to exact Lucide geometry (fetched from Lucide's repo at dev time,
  not redrawn from memory), keeping each `ICONS` key and its size class so visual weight
  matches the surrounding design.
- `taxi` and `heart` deleted.
- `aria-hidden="true"` + `focusable="false"` added to every icon SVG (all decorative,
  always beside a text label).
- Pure stroke throughout — the tinted `opacity=".12"` fills on `sickness`/`pension` were
  dropped so all icons share one Lucide treatment.

Diff is a single hunk (+37/−39) in `check.html`; nothing else in the file moved.

## Notable judgment calls

- **The abstract benefit icons were the real design work.** Lucide has no glyph named
  `maternity`, `paternity`, or `survivors`, and all three trend toward look-alike
  "person/family" shapes at 28px. They were chosen for mutual distinguishability rather
  than literal depiction: `baby` (maternity) vs `users-round` (paternity) vs
  `heart-handshake` (survivors) — an infant, a group, and a hand-and-heart read as three
  clearly different silhouettes. `invalidity` → `accessibility`, `sickness` →
  `heart-pulse`, `pension` → `piggy-bank`.
- **Stroke-width and size preserved per icon**, not normalised to Lucide's default (2).
  The design was tuned to 1.75 for content icons and 2/2.5 for chrome; keeping those
  values means the swap changed geometry only, not visual weight.
- **No new dependency.** Lucide path data was hand-embedded into the existing inline SVG
  strings. The prototype stays a dependency-free static file; the `@govtech-bb/react`
  `<Icon>` wrapper is a port-time concern only.
- **Other pages left alone deliberately.** `index.html`, `how-to.html`,
  `landing-page-how-to.html` each contain a single chrome SVG (logo/coat of arms), not a
  content-icon list — nothing to convert. Recorded as a deliberate no-op so it isn't
  revisited as drift.

## Artifacts

- `docs/icon-mapping.md` — concept → Lucide-name mapping, the source of truth for the
  React port (a lookup, not a re-decision).
- `docs/decisions/0001-icons-come-from-lucide-one-set-decorative.md` — records the
  Lucide-only, decorative-only convention as a constraint on future icon work.

## Verification

- **Structural (grep):** exactly 37 keys; `taxi`/`heart` absent everywhere; every SVG
  carries both `aria-hidden` and `focusable`; no dangling `ICONS.x` reference; every
  dynamic `icon:'...'` data value resolves to an existing key; `<svg>`/`</svg>` balanced.
  A general-purpose subagent re-ran all six checks independently — all clean.
- **Visual:** all 37 icons rendered at their real sizes (28px cards, 20–24px chrome) on a
  390px viewport via headless Chrome. Every icon reads clearly; the previously-ambiguous
  domain set is now recognisable and mutually distinct.
- The full wizard was served locally (`python3 -m http.server`) for user review.
