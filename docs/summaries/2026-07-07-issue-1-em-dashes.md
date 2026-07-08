# Issue #1 — Remove em-dashes from user-facing copy

## What changed

An editorial sweep of all three pages replacing every em-dash (U+2014) in
user-facing copy with punctuation chosen per instance, not a blind swap:

| File | Copy instances rewritten | Titles converted to en-dash |
|---|---|---|
| `index.html` | 19 | 1 (`<title>`) |
| `how-to.html` | 7 (two on one line) | 1 (`<title>`) |
| `check.html` | 22 (7 static HTML + 15 JS string literals) | 4 (`<title>` + 3 `document.title`) |

54 instances changed in total. The ~12 em-dashes inside HTML/JS comments were
left untouched — they are invisible to users and the issue allowed skipping them.

## Judgment calls

- **Titles use an en-dash, not a rewrite.** "Check your coverage – alpha.gov.bb"
  follows the platform's title convention; sentence-splitting a browser-tab
  title would read wrong. This includes the "Error: …" title set on validation
  failure.
- **Colons for definitions and examples.** The seven benefit definitions on
  `index.html` ("Sickness benefit: money to live on…"), the eight "who this is
  for" example lists, the two results headings ("Paying the minimum: about
  BDS$100 a month") and the progress label ("Step 1 of 3: Your work") all
  introduce an explanation, so a colon keeps them on one line.
- **Commas for Yes/No radio labels.** "Yes — I pay it" became "Yes, I pay it";
  a full stop would make the label feel like two utterances.
- **Full stops where the dash spliced two ideas.** e.g. "Takes about 3 minutes.
  Nothing is saved." and "You can start any time. You can also pay back up to
  three years you missed." — this matches the site's short-sentence style.
- **Small rephrases where dropping the dash left a fragment.** "— just yourself
  and your National Insurance number" became "You just need yourself and your
  National Insurance number" (both on `how-to.html` and in the `check.html`
  register branch, kept identical for consistency). The step-2 error message
  became "Enter how many good months. Use a number from 1 to 12" so the second
  half stays an instruction, not a dangling appositive.
- **Issue count vs. reality.** The issue counted 34 em-dashes in `check.html`;
  this branch is stacked on #29, whose results-block rewrite had already
  removed the one at old line 605, so 33 remained here.

## Verification

Playwright/Chromium drove all three pages via `file://`: the `check.html`
calculator was walked through all three steps, the step-2 validation error was
triggered (error title and message checked), and all three `paying` result
branches plus the good-months=12 "slow month" row were rendered. Every state
asserted zero U+2014 in `document.body.innerText`, en-dash titles, and zero
page errors — 55 checks passed. `grep -n '—'` now matches only code comments,
and a diff audit confirmed no attributes, IDs, classes or logic changed
(53 insertions, 53 deletions, all punctuation-in-text).
