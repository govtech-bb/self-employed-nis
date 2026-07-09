# 0001 — Icons come from Lucide, one set, decorative only

**Status:** Accepted · **Date:** 2026-07-08 · **Relates to:** issue #33

## Context

The wizard (`check.html`) originally rendered 39 hand-authored inline SVG icons.
Several were ambiguous at rendered size on mobile (28px cards, 20px chrome), and
some utility glyphs were near-verbatim copies of Lucide artwork — an inconsistent,
hand-maintained mix. The predecessor gig-worker prototype had the same problem and
tracked it as a defect (its issue #24). `@govtech-bb/react` ships a generic `<Icon>`
wrapper but provides no named icon library, so the artwork choice made here sets the
gov-bb precedent.

## Decision

All UI icons come from a **single established, licensed icon set: Lucide (ISC)**.

- **One set only.** Do not mix icon libraries and do not hand-author new icons. New
  icons are added by selecting the appropriate Lucide glyph.
- **Decorative by default.** Every icon SVG carries `aria-hidden="true"` and
  `focusable="false"`, and always sits next to a text label. No icon-only controls.
- **Consistent treatment.** Pure stroke; preserve the design's per-context size and
  stroke-width. No bespoke fills or effects.
- **Documented mapping.** The concept → Lucide-name mapping lives in
  `docs/icon-mapping.md` and is the source of truth for the React port — porting an
  icon is a lookup, not a fresh design decision.

## Consequences

- Future icon work (this prototype and the React port) must choose from Lucide and
  update `docs/icon-mapping.md` rather than drawing new artwork.
- Icons stay recognisable at mobile size and visually consistent across the service.
- If a needed concept has no literal Lucide glyph (e.g. `maternity`, `paternity`,
  `survivors`), pick a deliberately distinct semi-related glyph and record the choice
  in the mapping — do not fall back to hand-drawing.
