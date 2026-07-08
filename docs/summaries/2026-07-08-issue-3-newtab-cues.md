# Issue #3: Screen-reader "opens in a new tab" cues on external links

**Date:** 2026-07-08
**Branch:** `feat/issue-3-newtab-cues` (based on `dev`)

## Why

Six user-facing external links open a new tab without any warning in
their accessible name. A screen-reader user tabbing through links, or
pulling a links list, hears only the link text and the page changes
context silently (WCAG technique G201). Where a cue existed at all, it
was a separate paragraph near the link, which never reaches the link's
accessible name. The wizard's registration links, the primary CTA of
the whole journey, had no cue of any kind.

## Pattern decision

Option (b) from the issue: a **screen-reader-only suffix**. Each
external `target="_blank"` link gets a visually hidden span inside the
anchor:

```html
<span class="sr-only"> (opens in a new tab)</span>
```

The visible design does not change. The existing nearby paragraph cues
(`how-to.html:233`, `landing-page-how-to.html:319`) stay, since they
serve sighted users; the sr-only span serves the accessible name, so
the message is not duplicated for any one audience. All four pages
already define `.sr-only` (`check.html:80`, the other three at line
87), so no CSS was added.

## What changed

7 lines across 4 files; every change is inside the inner content of an
external `<a>` element. No structure, CSS, JS logic, or landmarks were
touched.

| Location | Link |
|---|---|
| `how-to.html:231` | "Register with NIS" button to the NIS portal |
| `landing-page-how-to.html:316` | CTA "Yes, I already have an NIS number" |
| `landing-page-how-to.html:317` | CTA "No, I've never registered" |
| `check.html:1319` | wizard registration-guide link, "yes" branch |
| `check.html:1320` | wizard registration-guide link, "no"/"unsure" branch |
| `check.html:1379` | wizard payment-options EZpay+ link |
| `index.html:300` | "Need to speak to someone?" `www.nis.gov.bb` link |

Footer Careers links (greenhouse.io) and the alpha banner are platform
chrome, out of scope per the issue, and were left untouched. Every
modified link already carried `rel` with `noopener`, so no `rel`
changes were needed. The `check.html` edits sit inside single-quoted
JS template strings; the cue text contains no apostrophes, so quoting
is unaffected.

## Verification

Playwright + Chromium against a local HTTP server, 43 assertions, all
passing. For each of the seven links (driving the wizard to the
registration-guide screen twice, once per register-path answer, and to
payment-options for EZpay+):

- Accessible name includes "(opens in a new tab)", asserted via
  Playwright's `getByRole('link', { name: ... })` accessible-name
  engine, plus a `textContent` check.
- Visible text unchanged: the link text with `.sr-only` descendants
  stripped equals the pre-change label exactly, and the span's
  rendered box is 1x1px, absolutely positioned, overflow hidden.
- `rel` contains `noopener`.
- Careers footer links contain no cue (chrome untouched).

All 14 wizard screens were rendered with zero page errors and zero
console errors, confirming the template-literal edits did not break
the app. The `landing-page-how-to.html` check opened the "Register
with NIS" disclosure so both CTAs actually rendered.
