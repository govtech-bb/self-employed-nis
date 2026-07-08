# Issue #29: Replace the "NIS can contact me" opt-in with information and NIS contact details

## Why

The calculator's results view ended with a "Keep this summary" block containing two
disabled checkboxes: "Email me my summary" and "NIS can contact me about making this
easier". Both were inert (nothing is collected or sent, see closed #6), so the block
made two promises the prototype cannot keep. The contact one was the worst: it implied
NIS would reach out to the user. The surrounding copy ("nothing is saved or sent yet...
The live version will store this...", "We won't share this with anyone.") only existed
to explain those checkboxes.

The issue originally scoped removal to the contact checkbox; the decision was upgraded
(option b) to remove both checkboxes and the whole inert opt-in concept, since an email
opt-in alone carries the same false promise.

## What changed

Only the `cc-optin` block in the results template of `check.html` was touched (a
parallel change was underway in the validation JS of the same file, so this change was
deliberately confined to that block).

- Removed both disabled checkboxes (`#opt-email`, `#opt-contact`) and their
  `cc-check` rows.
- Removed the now-false copy: the "nothing is saved or sent yet / the live version
  will store this" paragraph and "We won't share this with anyone."
- Kept the "Keep this summary" heading and the Print button; print is now the honest
  way to keep a summary, so the heading still reads true. Added a short lead-in:
  "Print this page so you have your numbers to hand."
- Added self-serve signposting instead of the contact-me offer, reusing verified copy
  from `index.html` rather than inventing new facts:
  - "Want to know more? Read [How to get covered]." (links to `how-to.html`)
  - "You can call NIS for free advice, or visit any NIS office. Phone: 467-4NIS (4647)."

The signposting paragraphs use body text, not the small italic `cc-est` style, because
they are actionable information rather than asides.

## Caveats

- 467-4NIS and office details still need NIS verification (flagged in the issue for
  the stakeholder review pack).
- The `cc-optin` block is hidden in print CSS, so the contact details do not appear on
  the printed summary. Left as-is to stay inside the guardrailed block; worth
  revisiting if a printed contact line is wanted.

## Verification

Playwright/Chromium drove `check.html` end to end (radios and number fields for all
three steps) and rendered results for all three `paying` branches (paying, notpaying,
no). All branches passed: no checkbox inputs in the results view, no "contact me" or
stale storage copy, phone number and working `how-to.html` link present, Print button
present, no page errors. Screenshots captured of the block and full results view.
