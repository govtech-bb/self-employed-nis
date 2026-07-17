# Split the registration steps into register.html

**Why:** `landing-page-how-to.html` was doing two jobs — pitching National
Insurance to self-employed workers *and* walking them through registration.
Splitting the walkthrough onto its own `register.html` keeps the landing page a
short pitch and gives the how-to content a linkable page of its own.

**Cut boundaries (owner-specified):** the block removed from the landing page
ran from the `<h2>How to get your benefits</h2>` (old line 299) through the
close of the "Source: NIS, June 2026…" container (old line 351) — steps 1–5
(including step 2's two NIS register-portal CTAs with their sr-only
"(opens in a new tab)" cues), "Missed a few years?", "Worked overseas?", the
"Need to speak to someone?" contact card, and the Source note. The sections on
either side ("Check what you would get", "Was this helpful?") stayed. The
inline late-payment-years script (old lines 387–405) moved with its
`#late-payment-years` span; the script block contained nothing else, so it
moved whole. Nothing on the landing page still references it.

**Heading promotions:** the moved `<h2>` becomes register.html's `<h1>`
(styled with the sibling pages' h1 classes); the moved `<h3>`s are promoted to
`<h2>` with their classes and text untouched, so the visual size stays 1.5rem —
matching the "Need to speak to someone?" heading, which was already an h2 and
stays one. Result: one h1 followed only by h2s, no skipped levels.

**register.html shell:** cloned verbatim from landing-page-how-to.html —
head/styles, skip link, official-website top bar, alpha banner, yellow header,
breadcrumbs (trail kept identical as placeholder chrome), footer and the
`comments.js` include. `<title>` follows the sibling pattern:
"How to get your benefits – alpha.gov.bb". No "Last updated" line or feedback
box was added: the brief was shell + moved content only.

**Landing page hand-off:** a closing "Ready to register?" section (before
"Was this helpful?") links to `register.html` using the page's existing
primary link-button pattern. Same-tab, no new-tab cue — it is internal
navigation. Copy is plain-language and em-dash free.

**Workflows:** `register.html` added to the `cp … dist/` list in both
`.github/workflows/deploy.yml` and `.github/workflows/pr-preview.yml`; without
the pr-preview change the page would 404 on every PR preview.

**Verified (Playwright + axe over local HTTP):** landing page carries none of
the moved strings, keeps its calculator and feedback sections, and its new
button click-navigates to register.html; register.html renders full chrome and
all moved content, `#late-payment-years` is populated ("2024 and 2025"), both
portal links keep their accessible-name new-tab cues and `rel="noopener"`;
heading order clean on both pages; axe (wcag2a/2aa/21aa/22aa) zero violations
on both; zero page errors once external hosts were stubbed — the only console
noise on this offline machine is `comments.js` failing to reach its Supabase
backend, reproduced identically on the untouched `how-to.html`, so it is
pre-existing and unrelated.
