# Issue #2: WCAG 2.2 AA pass (2026-07-08)

One PR covering the full accessibility pass from the re-grounded issue #2: landmarks on
all four pages, the missing skip link on `check.html`, the wizard's live-region/focus
model, keyboard support for the custom radio groups, the disabled-Continue pattern, and
the contrast candidates. Verified with axe-core 4.12 (wcag2a/2aa/21a/21aa/22aa +
best-practice) plus scripted focus and keyboard transcripts in Playwright.

## Judgment calls and why

### Landmarks: one `<header>` around all the page chrome

The axe `region` violation came from the "Official government website" top bar and the
alpha banner sitting in bare `<div>`s outside any landmark. Rather than inventing
`role="region"` labels for each strip (more landmark noise for screen-reader users to
skip past), the whole page chrome is now a single `<header>` (banner) landmark: top bar,
yellow logo bar, alpha banner. The three static pages already had a wrapper `<div>`
around exactly those elements, so there the fix is just promoting that wrapper to
`<header>` and demoting the inner yellow-bar `<header>` to a `<div>`. `check.html` had
no wrapper, so one was added, and its body grid changed from five rows to
`auto 1fr auto` to match its new three children.

### Live region out, focus-to-heading in

`#app` was `role="region" aria-live="polite"`, and every `render()` replaces the whole
screen with `innerHTML`, so screen readers could re-read the entire page on every radio
click. The live region is removed entirely rather than scoped: with focus management in
place it is redundant, because moving focus to the new screen's `<h1>` makes screen
readers announce the new context exactly once.

How it interacts with the existing render/scroll code (this is the part worth reading
before touching `render()` again):

- `nav()` now calls `render({ scroll: true, focusHeading: true })`. Scroll-to-top stays
  exactly as it was, and only on nav, which is the behaviour issue #20 cares about. The
  `<h1>` is focused with `preventScroll: true` so the focus move can never re-scroll
  after `scrollTo(0)`.
- The initial page load still calls `render({ scroll: true })` **without**
  `focusHeading`, deliberately: stealing focus on load is itself an accessibility bug.
- The error-summary focus (register form) keeps priority: if `state.errors` is
  non-empty and `#error-summary` exists, it gets focus and the heading does not.
- In-place re-renders (selecting a card/radio rebuilds the screen and used to dump
  focus on `<body>`): `render()` now records where focus was before the innerHTML swap
  and restores it after. Radios are identified by `data-rg`/`data-ri` (group index +
  option index, stamped by `setupRadioGroups()`); anything else with an `id` is restored
  by id. Restoration also uses `preventScroll: true`, so selecting an option neither
  moves focus nor scrolls.

### Radio groups: roving tabindex kept, not downgraded to native inputs

The issue offered "downgrade to `<input type=radio>`" as an alternative. The custom
buttons carry rich card layouts (icons, subtitles, price rows), and rebuilding those as
native inputs styled to match would be a much larger, riskier diff for a prototype. So
the ARIA pattern was completed instead: `setupRadioGroups()` runs after every render and
gives each group exactly one tab stop (the checked option, else the first), and a single
delegated `keydown` listener on `#app` implements Arrow keys (move **and** select,
wrapping), Home and End. Arrow selection focuses the target radio *before* clicking it,
so the post-render focus-restore lands on the equivalent new node. The two radiogroups
that had no accessible name (`register-path`, contact method) got `aria-label`s.

### Disabled Continue: `aria-disabled` + announced hint (not click-through errors)

Decision: keep the "not ready yet" look but switch from the `disabled` attribute to
`aria-disabled="true"`, keeping the button focusable; pressing it fills an adjacent
always-present `<p role="status">` with a specific hint ("Answer all three questions
above to continue."), so it is both shown and announced. Why not the pure GDS
"enabled + error summary" pattern: these wizard steps have no error-summary component
(only the register form does), and inventing one per screen is a bigger change than the
prototype warrants; the aria-disabled+hint pattern fixes the actual failure (silent,
unreachable, unexplained button) with a fraction of the surface. The hint clears on the
next re-render, i.e. as soon as the user picks something. The muted style needed a
contrast tweak (see below) because `:disabled` styling no longer applies.

### Contrast: new `bb-yellow-dark` text token

Per-screen axe scans (the 4-page scan only ever sees the landing screen of the SPA)
flagged every place `bb-yellow-00` (#e8a833, 2.1:1 on white) was used as *text*: tier
chips and the Minimum price on `plan`, tags on `registration-guide` and
`payment-options`, and the tracker's due/in-progress labels. Fix: a `bb-yellow-dark`
(#8a5a00, 5.6:1 on yellow-10) token used wherever yellow is text; icons keep their
accent colours. The tracker "On track" chip (`bg-green-100` + `text-green-00`, 3:1)
moved to the `green-10` background the other eligibility chips already use. The
aria-disabled button style is grey-00 background + mid-grey text (5.4:1) instead of the
old white-on-#99a8cc (2.2:1), since an aria-disabled control is arguably no longer
exempt from contrast rules.

### Small fixes riding along

- Tracker screen had no `<h1>` (axe `page-has-heading-one`, and nothing for
  focus-to-heading to land on): the "Welcome back, Marcus" name line is now the `<h1>`,
  visually unchanged.
- Progress bar: `aria-label` moved onto the actual `role="progressbar"` element (axe
  `aria-progressbar-name`), plus `aria-valuetext="Step N of 6"`.
- `prefers-reduced-motion: reduce` disables the screen fade/slide and bar-fill/card
  transitions (issue item 9).

## Verification evidence

Baseline before changes, re-scan after; scripts in the session scratchpad
(`axe-scan-issue2.js`, `axe-screens-issue2.js`, `verify-issue2-transcripts.js`).

- **4 pages x mobile+desktop**: baseline had 1 violation type per page (`region`,
  2 nodes on `check.html`); after: **0 violations everywhere**.
- **All 14 wizard screens (+ plan-with-tier, register-with-errors)**: baseline had
  `region` x2 on every screen, `aria-progressbar-name` on the 6 journey screens,
  `color-contrast` on plan/registration-guide/payment-options/tracker, and
  `page-has-heading-one` on tracker; after: **0 violations on every screen**, zero page
  errors. (Scan gotcha: axe must run after the 0.32s screen fade finishes, or
  mid-animation opacity produces dozens of phantom contrast failures.)
- **Focus transcript**: `nav()` puts focus on the new screen's `<h1>` on all 14 screens;
  selecting a worker card, main-job radio, dependants radio and plan tier keeps focus on
  the selected control with scroll position unchanged; load does not steal focus.
- **Keyboard transcript** (type-of-work 11 options, main-job 2 options): Tab enters each
  group once on its checked/first option, arrows move+select (wrapping), Home/End jump,
  Tab leaves to the next group / Continue without stopping on other options.
- **Skip link**: first Tab on `check.html` lands on it, Enter moves focus to
  `<main id="main">`; same re-verified on the three static pages after the header
  restructure.

## Deliberately not done

- Issue #20 (scroll jump) overlaps the render/scroll code touched here; per the issue
  owner, #20 stays open for their own re-test. The existing instant scroll-to-top-on-nav
  behaviour was preserved untouched.
- External links (`target="_blank"`) were not modified; a parallel change is adding
  sr-only context inside them.
- VoiceOver pass is still a manual step; the scripted checks cover the mechanics
  (focus order, announcements via role/status semantics) but not actual SR output.
