# Restore the content-page calculator CTAs (issue #50)

**Why:** nothing on the live site linked to the calculator (`check.html`) — it
was reachable only by typing the URL. Both content pages already carried a
ready-made "Check what you would get" section with a `check.html` button, but
the block was wrapped in an HTML comment, severing the funnel's Interest →
Action bridge. Restoring simply removes the comment markers.

**Why a plain un-comment, not a flag:** these are static HTML pages, not the
JS-templated wizard, so there is no natural home for a `SHOW_*` const (the #48
precedent) and no stated need for a runtime switch. The `HIDDEN FOR NOW …`
descriptive comment above each block was deleted too, so no stale "hidden" note
remains.

**Scope calls (owner decisions):**
- Restored on **both** `index.html` and `landing-page-how-to.html`; the two
  blocks were byte-identical.
- `how-to.html` deliberately left without a calculator link.
- **Copy left as-is on purpose.** The restored text predates the #1 / PR #39
  em-dash sweep and still contains an em-dash ("for a few weeks — and what
  National Insurance would pay you") plus an unverified "It takes about 3
  minutes" claim. The owner chose to defer the copy fix; both need catching in
  the stakeholder content review rather than shipping as house-rule-clean.

**Verified:** no `HIDDEN FOR NOW` markers remain on either page; each page now
has exactly one live `href="check.html"` link into the calculator, which is
present in the repo; `how-to.html` untouched. Branched off `dev`; rebased onto
`origin/dev` (which had moved ahead via #52/#51 without touching these two
files, so no conflict).
