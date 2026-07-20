# Lean-estimator variant (branch `variant/lean-estimator`)

## Why this branch exists

The MDAs are choosing between two shapes of the NIS coverage wizard:

- **The full journey (dev):** six steps. It asks about work type, a main job,
  dependants, savings and current NIS payments as well as earnings.
- **The lean journey (this branch):** four steps. It asks only the questions
  the estimate actually uses: good month, slow month, good months per year
  (all validated per #45/#47), the contribution tier pick, and the
  NISSS-number routing question on the register path.

The audit behind this (issues #31, #54 to #57) found that work type, main job,
dependants and count, savings, and paying-NIS influence nothing downstream.
The lean variant removes the asking, not the screens, so the two journeys can
be compared side by side for as long as the decision takes.

## The flag design

Everything hangs off one constant near the top of the script in `check.html`,
beside `SHOW_TRACKER_ENTRY` (the precedent for hide-don't-delete):

```js
const LEAN_ESTIMATOR = true;
```

Flip it to `false` and the full six-step journey is restored exactly as on
dev. No screens were deleted and no code was restructured; every behavioural
difference is a small conditional on the flag:

| Where | When `LEAN_ESTIMATOR` is true |
| --- | --- |
| `JOURNEY` | `['income-reality', 'risk-awareness', 'plan', 'next-steps']`. The progress bar derives Step X of Y from `JOURNEY`, so it shows "Step 1 of 4" etc. with no further changes. |
| Landing CTA "Estimate my contributions" | targets `income-reality` instead of `worker-type` (same conditional covers the identical CTA on the benefits quick-reference page) |
| `income-reality` Back | goes to `landing` instead of `worker-type` |
| `nextFromIncome()` | navigates to `risk-awareness` instead of `your-situation` |
| `risk-awareness` Back | goes to `income-reality` instead of `your-situation` |
| `screenRegisterPath()` Back proxy | the full journey uses `state.data.workerType` to tell "arrived via the journey" from "arrived from the landing page"; the lean journey never sets it, so the tier pick (`state.data.contributionTier`, gated on the plan screen) is the proxy instead |

`worker-type` and `your-situation` stay registered in `SCREENS` and `TITLES`
but become unreachable: no visible control on any reachable screen navigates
to them. Their own internals (worker cards, dependants count validation, the
gated Continue) are untouched.

## What is deliberately unchanged

- All of #47's input validation and the insurable-earnings ceiling note.
- The seasonality maths (#38): `goodMonthsValue()` still feeds the risk and
  plan formulas, and the lean journey still requires and validates the
  good-months answer before either formula runs.
- The plan, next-steps, register-path, payment-options, contact and
  benefits-quick flows, and the landing page's benefits and register links.

## How it was verified

Playwright and Chromium against a local static server, plus axe-core
(wcag2a/wcag2aa/wcag21aa/wcag22aa):

- Lean journey clicked end to end, with progress text asserted at each step,
  a validation error exercised (blur an emptied field, then Continue) and all
  three register-path answers routed correctly.
- Unreachability sweep across every reachable screen: no visible control's
  onclick targets `worker-type` or `your-situation`, and no Back chain lands
  there.
- Flag-flip regression: with `LEAN_ESTIMATOR = false` in a scratch copy, the
  full journey is intact (Step 1 of 6 at worker-type, your-situation
  reachable, all Backs as on dev).
- Maths sanity: gm=3 vs gm=11 with the same amounts produce different plan
  averages (BDS$1,225 vs BDS$2,358 at 2,500/800).
- axe: zero violations on the four lean journey screens; zero page errors.
