# 2026-07-09 — Issue #45: validate the calculator's number inputs

Branch `feat/issue-45-input-validation`, stacked on `feat/issue-2-wcag-pass`
(PR #41). Merge order: PR #41 first, then this. No reviewer was present this
session, so the strategy decisions below were made autonomously against the
issue's case table and are recorded here with their reasoning.

## The problem

All four number inputs (good month, slow month, good months per year,
dependants count) silently clamped state while the visible field kept whatever
was typed. Type `-500` and the estimate quietly used 0; type `1e3` and
`parseInt` read it as 1. The income screen had no validation surface at all.

## The strategy: normalise the benign, error the invalid

One rule drove every per-case decision: **the estimate must never use a number
the user did not visibly see.** From that, two branches:

- **Normalise visibly** when the user's intent is unambiguous: `1e3` means
  1000, `8.5` dollars means 9 (nearest whole), a pasted `"2,500"` / `"$2500"` /
  `"BDS$1,200"` means the digits. The write-back happens on change/Continue so
  the field, the big display, the slider and `state.data` all show the same
  number. This is deliberately the benign half of #4 (clamp/write-back):
  **#4 should be re-scoped or closed against this change when it merges.**
- **Error on Continue** when intent is ambiguous or wrong: negative, over max,
  empty required, unreadable (`validity.badInput`), fractional months/people,
  zero on min-1 fields. Validation is GDS-orthodox (on Continue, not on every
  keystroke) and reuses the register screen's pattern wholesale: `state.errors`,
  the focusable `#error-summary` (extracted into a shared `errorSummary()`
  helper rather than duplicated), the `Error:` title prefix, and PR #41's
  `focusErrors` rule (summary focused only on a submit-caused render).

## Per-case decisions

| Case | Money fields (0–20000) | Months (1–12, optional) | Dependants (1–20, gated) |
|---|---|---|---|
| `-500` | Error: "Enter an amount between BDS$0 and BDS$20,000." | Error: "Enter a whole number of months from 1 to 12." | Inline error, gate closed |
| empty | Error: "Enter your good/slow month amount." | Allowed (optional, see below) | Gate closed, PR #41 hint (unchanged) |
| badInput (`12e`, pasted junk) | Error: "…using numbers only." | Months error | Inline "digits only" error, gate closed |
| `1e3` | Normalised to 1000 | `1e1` → 10 valid | Normalised if valid, else inline error |
| `8.5` | Rounded to 9, visibly | Error (fractional months are not roundable intent) | Inline error (fractional people) |
| over max | Error | Error | Inline error, gate closed |
| `0` | Valid (min is 0) | Error (min is 1) | Gate closed (existing behaviour) |
| `"2,500"` paste | Stripped to 2500, accepted | Stripped, then range-checked | Stripped, then range-checked |

## Decisions with non-obvious reasoning

- **State mirrors the field exactly, including out-of-range values.** The old
  clamp is gone; `state.data.goodMonth` can briefly hold `-500` or `null`
  (empty/badInput) while the user edits. That is safe because every screen
  that consumes these numbers sits behind `nextFromIncome()` or the
  your-situation gate, and it is what makes "field and state never disagree"
  literally true rather than approximately true.
- **`state.raw` echo.** A number input's `.value` reads as empty under
  badInput, and an error re-render rebuilds the screen from state. Without
  keeping the raw typed string, re-rendering would launder "-500" into a clean
  number next to an error saying the number is wrong. Readable-but-invalid
  text is echoed back; genuinely unreadable text cannot be (the browser hides
  it), so those fields re-render empty with the error explaining why.
- **Paste interception, not value parsing.** Chromium's number inputs refuse
  formatted text before JS ever sees it, so `"2,500"` cannot be normalised
  after the fact. A `paste` handler reads the clipboard, strips `$`, commas,
  spaces and a `BDS` prefix, and lands plain digits; anything still
  non-numeric falls through to the browser and the Continue-time error.
- **dependantsCount integrates with PR #41's gate instead of a second submit
  path.** The screen's Continue is aria-disabled with a `role=status` hint, so
  a Continue-time error summary is unreachable by design. Invalid input keeps
  `state.data.dependantsCount` at 0 (gate stays closed), shows an inline
  field error + `aria-invalid` updated in place (a full `render()` would fight
  the caret — same reason PR #41 gave `situationContinue()` its own holder),
  and the gate hint names the actual blocker ("Enter a whole number of people
  from 1 to 20 to continue."). The raw echo also survives mid-screen
  re-renders (clicking the savings radio no longer wipes an invalid count).
- **`goodMonthsPerYear` stays optional and stays out of the maths.** Issue #38
  owns the dead field. Requiring it here would entrench it; leaving typed
  values unvalidated would repeat the bug. So: empty passes, anything typed
  must be a whole number 1 to 12, and nothing new reads it.
- **Empty money display shows nothing** (not BDS$0, not a dash) while the
  field is empty or unreadable — showing a substituted number is the exact
  bug this issue removes, and the repo's em-dash rule makes a dash placeholder
  unattractive.
- **Deferred plan-skill steps:** the "ready" label step and ADR proposal were
  skipped (no reviewer present; parent session owns issue labels and PRs).
  The plan file `docs/plans/issue-45-input-validation.md` is intentionally
  uncommitted per repo convention.

## Verification

Playwright + Chromium against the real `check.html`: 139 checks, all passing.
Full matrix per field (`-500 / abc / 12abc / 12e / 1e3 / empty / 0 / 8.5 /
50000 / "2,500" / "$2500" / "BDS$1,200" / paste variants`, real typed
keystrokes and real ClipboardEvent pastes), field/state agreement asserted per
case, Continue blocked/allowed per the table, error summary focus + `Error:`
title + `aria-invalid`/`aria-describedby` asserted, spinner arrows confirmed
clamped natively. Regression: all 14 screens render with zero page errors;
axe (WCAG 2.x A/AA) shows zero violations on every screen and on the income
error state — matching the base, once the harness waits for Tailwind's CDN
JIT to settle (unsettled runs produce phantom color-contrast hits on both
base and branch alike).

## Revision: no upper cap, ceiling disclaimer instead (owner decision)

The BDS$20,000 upper bound was an arbitrary sanity cap inherited from the
first prototype, not a NIS figure. Removed entirely: money fields now accept
any amount (max attribute dropped, over-max error deleted; negative-only
error copy). Instead, entries above the real domain ceiling
(NIS.MAX_MONTHLY_INSURABLE, BDS$5,360/month insurable earnings) reveal a
per-field role=status note: "The most NIS can insure is BDS$5,360 a month.
Amounts above this do not change your estimate." Announced once on the
hidden-to-shown transition (setLiveRegion), hidden again when the value drops
to the ceiling or below; rendered from state so it survives re-renders. The
estimate maths already capped insurable earnings at the ceiling, so this is
purely an honesty fix at the input layer.

## Revision: blur-time validation, all questions required (owner decision)

Validation timing moved from submit to blur: each income field validates as
the user leaves it, updating its inline error, aria-invalid, border and the
Error: title prefix in place (no re-render, so focus and carets are never
disturbed). Continue re-runs the same per-field rules and focuses the first
invalid field; the income screen's submit-time error summary is gone (errors
now live at the fields), and the then-unused errorSummary() helper was
removed. goodMonthsPerYear flipped from optional to required ("Enter how many
good months you have in a year.") - overriding the earlier keep-it-optional
choice; note this makes resolving #38 (the maths ignores the answer) more
pressing, since users are now forced to answer a question that changes
nothing. Radio screens keep their aria-disabled gate + hint pattern, which
already enforces required answers.
