# Issue #4 — Reconcile the calculator's number inputs on change

## Why this existed

The coverage calculator's whole value is a dollar figure the user trusts. Issue #4
verified that while the step-2 validation gate already blocks empty, non-numeric
and negative earnings, nothing ever wrote a corrected value back into the visible
fields — so inputs that *pass* validation could still disagree with the maths:
`8.5` good months displayed as 8.5 while `parseInt` fed 8 into the estimate, and
`1e3` displayed as scientific notation while the estimate used 1000. The number on
screen and the number in the sums silently diverged, which is exactly the class of
bug nobody catches in review.

## What was decided, and why

**Reconcile on `change`, only for values the gate would accept.** A new
`reconcileField()` (end of the script in `check.html`) parses the committed value
with the *same semantics `showResult()` uses* — `parseFloat` for `#good`/`#lean`,
`parseInt` for `#goodmo` — clamps to the field's min/max, and writes the result
back into the input. Field, state (`S.good`/`S.lean`/`S.goodmo`) and the rendered
estimate can therefore never disagree.

Three deliberate choices:

1. **Invalid input is left untouched.** If the parse fails validation (empty,
   NaN, negative, good months outside 1–12), reconcile does nothing and the
   existing `next(2)` error summary remains the guard. Clamping `-500` to `0`
   would have silently "fixed" nonsense into a number the user never typed and
   let them through — the issue's acceptance criteria explicitly forbid that
   (`-500`/`abc`/empty must never reach results).
2. **`8.5` good months becomes `8`, not `9`.** `parseInt` truncation is what the
   estimate has always used, and the issue's manual spot-check names 8 as the
   expected outcome. Preserving those semantics means the fix changes only the
   display, never the maths. (Same logic makes `1e3` good months reconcile to
   `1` — parseInt has always read it as 1; now the field shows it.)
3. **Earnings round to cents, not to the `step="50"`.** The step is a spinner
   increment, not a validation rule ("rough figures are fine"); the maths accepts
   any float, so snapping 3025 to 3050 would alter honest input for no gain.

Listener is on `change` (commit-time), not `input`, so typing is never rewritten
mid-keystroke — verified by dispatching input-only events.

## Verification

Headless Chromium (Playwright, `file://` load) drove all three fields through
`-500`, `abc`, empty, `8.5`, `1e3` and a valid value, dispatching real
`input`/`change` events, then clicked Continue and (where reachable) rendered
results. Before the fix: 4 disagreement failures (the two documented gaps in each
form). After: all cases pass — invalid values still show the error summary and
never advance; every value that reaches results has visible field == state ==
the "(×N)" figure in the breakdown.

## Scope note

Only the reconcile block was added (single hunk after `restart()`); no copy,
templates, validation messages or other pages were touched, keeping clear of the
parallel results-template work in #5's region.
