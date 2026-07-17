# Issue #1: Remove em-dashes from user-facing copy (wizard branch)

**Date:** 2026-07-08
**Branch:** `feat/issue-1-em-dashes-wizard` (based on `feature/nis-prototype-updates`)

## Why

Em-dashes splice two ideas into one long sentence, which works against the
plain-language style these pages are written in. The issue was re-grounded
against this branch on 2026-07-08: 150 em-dashes total, of which 100 are
user-facing copy and 3 are page-title separators. The rest are HTML/CSS/JS
comments, which are not user-facing and were deliberately left alone.

## What changed

Each user-facing em-dash was rewritten individually, not regex-swapped.
Per instance the choice was a full stop (two ideas), a comma (an aside or
tail clause), or a colon (a list or an example follows). No em-dash became
a bare hyphen.

| File | Copy rewritten | Title | Comments left |
|---|---|---|---|
| `index.html` | 19 | 1 (em → en dash) | 6 |
| `how-to.html` | 7 | 1 (em → en dash) | 2 |
| `check.html` | 41 (all inside JS template literals) | 0 (already en-dash) | 33 |
| `landing-page-how-to.html` | 33 (incl. 2 register CTA labels) | 1 (em → en dash) | 6 |

## Notable judgment calls

- **Benefit lists** (`index.html`, `landing-page-how-to.html`): the
  `<strong>Benefit name</strong> — description` pattern became a colon,
  keeping the name scannable as a label.
- **Yes/No option and CTA labels** ("Yes — I pay it", "No — I've never
  registered", etc.): a comma keeps them scannable as labels without
  turning a button into two sentences.
- **Tracker placeholder** (`check.html:1669`): the `'—'` shown in the
  amount column for a skipped month was a legitimate typographic dash,
  but since a "Skipped" status label sits directly beneath it, it was
  replaced with the explicit word "None" (the option the issue offered).
  This also lets automated checks assert zero em-dashes on every screen.
- **`check.html:954` tier card**: the rewrite introduced "It's" into a
  single-quoted JS string, which broke the page (caught immediately by
  the runtime check, a SyntaxError blanked the wizard). Fixed by switching
  the string to double quotes. This is exactly why verification ran the
  app rather than trusting grep.
- **`landing-page-how-to.html:336`**: the parenthetical pair
  ("history — every payment over your working life — and…") could not
  become a comma pair without reading as a three-item list, so the aside
  was moved to its own short sentence.
- **Hidden calculator blocks** (`index.html:284`,
  `landing-page-how-to.html:297`): these lines contain copy but sit inside
  `<!-- HIDDEN FOR NOW -->` comment blocks, so they render nowhere and were
  counted as comments, matching the issue's classification.

## Verification

Playwright + Chromium drove all four pages via `file://`:

- `index.html`, `how-to.html`, `landing-page-how-to.html` (with the
  "Register with NIS" disclosure opened so both CTA labels rendered).
- `check.html`: all 14 screens in the `SCREENS` map via `nav()`, plus a
  worker-type selection, each of the three contribution tiers via
  `selectTier()` (confirming the tier-dependent benefit copy rendered),
  all risk cards expanded, each register-path answer's registration
  guide, and an empty submit of the contact form to sweep its validation
  error copy.

Every check asserted zero U+2014 in `document.body.innerText`, zero page
errors, and an en-dash `document.title`. All passed. A diff audit
confirmed the 100 removed / 100 added lines carry identical attribute
tokens (id/class/onclick/href/etc.), i.e. the change is punctuation and
copy only. `grep -n '—'` across the four files matches the 47 expected
comment survivors exactly.
