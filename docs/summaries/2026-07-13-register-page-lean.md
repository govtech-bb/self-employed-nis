# Register page split (branch `feat/register-page-lean`)

## What changed

The registration content on `landing-page-how-to.html` moved to a new
standalone page, `register.html`. This is the lean-branch copy of the same
change being made on dev, kept as mechanically identical as possible so a
future dev-to-lean merge reconciles trivially. `check.html` (and its
`LEAN_ESTIMATOR` flag) was not touched.

### `landing-page-how-to.html`

- Removed the whole "How to get your benefits" block: steps 1 to 5
  (including the register-portal chooser with its two NIS links),
  "Missed a few years? It's not too late", "Worked overseas?", the
  "Need to speak to someone?" contact card, and the Source paragraph.
- Removed the inline late-payment-years script, which only served that block.
- Added a short closing section before "Was this helpful?":
  an h2 "Ready to register?", one plain-language line, and a same-tab
  primary link-button ("How to get your benefits") to `register.html`.
- "Check what you would get" and "Was this helpful?" are unchanged.

### `register.html` (new)

- Clones the landing page's shell verbatim: head and styles, skip link,
  official banner, alpha banner, yellow header, breadcrumbs (identical
  trail), footer, and the `comments.js` include.
- Title: `How to get your benefits – alpha.gov.bb` (sibling-page pattern).
- The moved h2 became the page h1; the moved h3s were promoted to h2
  (tag change only, classes kept) so the outline is a single h1 followed
  by h2s with no skips. The "Need to speak to someone?" heading was
  already h2 and stays. Content text is verbatim.
- The late-payment-years script moved here with its content, so
  `#late-payment-years` keeps updating each year.

### Workflows

- `.github/workflows/deploy.yml` and `.github/workflows/pr-preview.yml`:
  `register.html` added to both `cp ... dist/` publish lists.

## Verification

Playwright plus Chromium against a local HTTP server, plus axe
(wcag2a/2aa/21aa/22aa). All checks passed:

- Landing page: none of the moved strings remain; "Ready to register?"
  renders and its link navigates to `register.html`; the estimator CTA and
  feedback box are intact; clean heading order; zero axe violations; zero
  page errors.
- Register page: full chrome renders; all moved content present;
  `#late-payment-years` shows "2024 and 2025"; both NIS portal links keep
  their "(opens in a new tab)" accessible-name cue and `rel="noopener"`;
  single h1 then h2s; zero axe violations; zero page errors.
- Lean wizard sanity: `check.html` still loads, and its landing CTA lands
  on the earnings screen at "Step 1 of 4" with zero errors.
