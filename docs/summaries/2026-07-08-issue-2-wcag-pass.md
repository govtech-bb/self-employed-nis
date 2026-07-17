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

## Review fixes (same day, after adversarial review of this branch's own work)

An adversarial review of commit `666c00e` produced nine empirically confirmed findings,
all in `check.html`. Each was reproduced on the pre-fix code and re-verified fixed with
Playwright (`verify-review-fixes.js` in the session scratchpad runs the same checks in
`before` and `after` mode). Full regression after the fixes: axe 0 violations on all
4 pages (mobile+desktop) and all 14 screens (+ plan-with-tier, register-with-errors),
focus/keyboard/skip-link transcripts all green, zero page errors.

1. **Register-screen error focus steal.** After a failed submit, picking a contact
   method re-rendered with `state.errors` still set, and `render()` re-focused
   `#error-summary`, making the radio group inoperable. Fix: `render()` only focuses the
   error summary when called with `focusErrors: true` (set solely by `submitForm()`),
   and answering the contact-method question deletes its own error
   (`selectContactMethod()`). Other errors intentionally persist until the next submit,
   matching the usual validate-on-submit model.
2. **Reduced-motion block was dead for transitions.** It preceded the base
   `.selectable`/`.chev`/`.protect-bar-fill` rules, so at equal specificity the base
   transitions won. The block now sits at the END of the stylesheet (a comment explains
   the source-order dependency). Judgment call: a standard catch-all
   (`* { animation-duration: 0.01ms; transition-duration: 0.01ms }` etc.) was added to
   the block, because verification showed Tailwind's own utility transitions
   (`transition-all` on input wrappers) still animating under reduced motion; nothing in
   the page depends on transition timing. Verified with `reducedMotion: 'reduce'`:
   computed transitions are `none` on the three named classes, and no animation longer
   than 0.01ms exists on selection or nav (the Tailwind CDN JIT briefly spawns 0.01ms
   transitions when it injects newly generated CSS; nothing is running 250ms later).
3. **Modifier-key hijack.** The radio keydown handler now returns early when
   Ctrl/Cmd/Alt is held, so Ctrl+Home / Cmd+Arrow scroll shortcuts no longer change
   answers.
4. **Arrow-scanning wiped the typed dependants count.** Preserved-count semantics
   chosen: `dependantsCount` still zeroes while "No" is selected (downstream code keeps
   its invariant that the count is only meaningful under "Yes"), but the last non-zero
   value is stashed in `state.data.lastDependantsCount` and restored when the user
   returns to "Yes"; a first-time "Yes" still defaults to 1. `restart()` clears it
   naturally by rebuilding `state.data`.
5. **Silent reveals.** A single persistent `<p id="reveal-status" role="status">` lives
   outside `#app` (so re-renders cannot destroy it mid-announcement). Wording, kept
   short and only fired when content actually appears: first tier pick announces
   "Your plan details are shown below." (later tier changes are quiet; the radio label
   itself announces the change); switching to "Yes" on dependants announces "A question
   about how many people depend on you was added below." Announce uses clear-then-set on
   the next frame so identical messages re-announce.
6. **your-situation stale Continue + wrong hint.** `updateDependantsCount()` now
   refreshes the Continue button in place (the button moved into
   `#situation-continue`, rebuilt by the shared `situationContinue()` helper), an
   in-place refresh rather than a full `render()` so the caret in the count field is
   never disturbed while typing. The hint now names the real blocker: unanswered radios
   get "Answer all the questions above to continue.", a cleared count gets "Enter how
   many people depend on your income to continue."
7. **Entrance animation replayed on every selection re-render.** `render()` toggles a
   `no-anim` class on `#app` for any render not triggered by navigation
   (`opts.scroll`), and `.no-anim .screen { animation: none; }` suppresses the fade.
   Nav (and initial load) still animate once; error re-renders no longer flash either.
8. **Safari/VoiceOver focus-restore gap.** A capture-phase click listener on `#app`
   records the activated radio's `data-rg`/`data-ri` before the inline handlers call
   `render()`; `render()` prefers that record and falls back to `activeElement`
   (keyboard activation). One central listener instead of touching every selection
   handler. Verified by dispatching a click without focusing (Safari's mouse-click
   behaviour): focus previously fell to `<body>`, now lands on the clicked radio.
9. **Second press of a not-ready Continue was silent.** `showDisabledHint()` clears the
   `role=status` element and re-sets the text on the next animation frame, so AT does
   not dedupe the repeat announcement. The DOM mutation log confirms text / empty /
   text across two presses.

Out of scope, per the review disposition: the `comments.js` re-scoping via `id="main"`
(finding #8 of the review) is documented on the PR as an accepted trade-off and was left
untouched. External links' inner content belongs to a parallel PR and was not modified.

## Deliberately not done

- Issue #20 (scroll jump) overlaps the render/scroll code touched here; per the issue
  owner, #20 stays open for their own re-test. The existing instant scroll-to-top-on-nav
  behaviour was preserved untouched.
- External links (`target="_blank"`) were not modified; a parallel change is adding
  sr-only context inside them.
- VoiceOver pass is still a manual step; the scripted checks cover the mechanics
  (focus order, announcements via role/status semantics) but not actual SR output.

## Dev-sync + second review batch (2026-07-09)

### Merging origin/dev

Dev gained two merges after this branch was cut: PR #43 (issue #31, direct
NISSS-number routing) and PR #44 (issue #33, Lucide icons). PR #43 went further
than a routing tweak: under the "interactive tool collects no data" decision it
deleted `screenRegistrationGuide`, `screenRegister`, `screenConfirmation`,
`goToRegistrationGuide`, `selectContactMethod`, `submitForm`, `restart`, and the
`firstName`/`contact`/`contactMethod` state, leaving a 12-screen map. Three
conflict regions in `check.html`, all resolved toward dev's routing:

| Region | Resolution |
| --- | --- |
| register-path Continue | Kept dev's answer-based routing (`primaryLink` to the NIS form for yes/no, `nav('contact')` for unsure). Carried this branch's aria-disabled + hint pattern onto the unanswered default case, so the gated Continue still explains itself when pressed. |
| screenRegister form (contact field + contact-method radiogroup) | Kept dev's deletion. This branch's radiogroup `aria-label` and `selectContactMethod` error-clearing edits are moot. The chip contrast fix this branch made in the deleted registration guide was carried over to the surviving `screenContact` "ways to reach them" chips (`text-bb-yellow-00` on `bg-bb-yellow-10` fails contrast; now `text-bb-yellow-dark`). |
| goToRegistrationGuide / selectContactMethod / submitForm / restart | Kept dev's deletion; this branch's `render({ focusErrors: true })` submit change goes with them. The generic error plumbing in `render()` stays as dev has it. |

Post-merge integrity, verified in Chromium: 12 screens, `registration-guide`
absent from the map, `goToRegistrationGuide` undefined, every `nav()` target in
the file is a SCREENS key, all screens render with Lucide icons and zero page
errors.

### Second review batch (5 findings)

1. **Dead `#contactMethod` error-summary link — moot after the sync.** The
   register form, its error summary, and the `contactMethod` radiogroup were all
   deleted on dev; no reference survives in the merged file.
2. **Silent tier changes.** `selectTier()` now announces every tier CHANGE: the
   first pick keeps "Your plan details are shown below.", later changes say
   "Estimates updated for the <tier> plan." Re-picking the already-selected tier
   announces nothing.
3. **Tracker h1 lacked context.** The h1 was the bare mock name ("Marcus"), so
   the focused heading told screen-reader users nothing about the screen. It now
   reads "Your NISSS contributions" (matching the page title), with the name
   moved into the "Welcome back, Marcus" subtitle above it. Same card layout.
4. **Duplicate live-region helpers.** `showDisabledHint()` and `announce()` both
   implemented the clear-then-requestAnimationFrame write. Extracted a shared
   `setLiveRegion(el, msg)` used by both.
5. **Redundant reduced-motion rules.** The specific `.screen`,
   `.protect-bar-fill`, `.selectable`, `.chev` rules inside the
   prefers-reduced-motion block were subsumed by the `*, ::before, ::after`
   `!important` catch-all and were removed. Under emulated reduced motion,
   `.selectable`'s computed transition-duration and `.screen`'s
   animation-duration are 0.01ms (imperceptible; duration-based rather than
   `transition-property: none`, so any code waiting on transitionend keeps
   working).

### Verification

Playwright + Chromium + axe, mobile viewport (390x844): 80/80 checks pass.
axe (wcag2a/2aa/21a/21aa/22a/22aa + best-practice) reports zero violations on
all 12 wizard screens and all 4 pages (index, check, how-to,
landing-page-how-to). Skip link, radio arrow-key selection with focus retention,
gated-Continue hint announcement, register-path routing for all four answer
states (including sr-only new-tab cues on the NIS links), and reduced-motion
behaviour all re-verified after the merge.
