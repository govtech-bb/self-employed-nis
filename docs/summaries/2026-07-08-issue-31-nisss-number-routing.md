# Issue #31: Fix "Do you already have an NISSS number?" routing

**Date:** 2026-07-08
**Branch:** `feat/issue-31-nisss-number-routing` (based on `dev`)
**File:** `check.html`

## Why

The register-path screen ("Do you already have an NISSS number?") asked a routing
question but funnelled everyone through an intermediate "How to register" guide,
and its `unsure` answer was routed identically to `no` — straight toward the
new-applicant registration form. A user who is unsure may already have a number;
pushing them to the new-applicant form online risks a duplicate registration. The
right first step for them is to contact NIS and check their status.

Separately, the journey ended in a "Tell us where to reach you" form that collected
a name, contact detail, and preferred contact method, then showed a confirmation
screen with a fabricated reference number. For a backend-less prototype that is
dishonest and collects data it cannot act on. The decision was to make the user
initiate contact instead, and keep the frontend focused. See
`docs/decisions/0001-interactive-tool-collects-no-data.md`.

## What changed

**Register-path Continue now routes per answer**, swapping the Continue control
itself on selection (the screen already re-renders via `setHasNis()`):

| Answer | Continue becomes |
|---|---|
| Yes, I have one | real external `<a>` (new tab) → `…/self-employment-registration-form-page/` |
| No, I never registered | real external `<a>` (new tab) → `…/self-employment-registration-form-new-nis-applicant/` |
| I'm not sure | internal button → new `contact` screen |
| *(none)* | disabled Continue |

The two NIS URLs already existed in the old guide's "Online" card; this promotes
that routing up to the Continue button. A real `<a>` (not `window.open`) preserves
keyboard, middle-click, and screen-reader semantics — a new `primaryLink()` helper
was added for it (the repo previously only had `primaryBtn()`).

**New static `contact` screen replaces the register form.** It lists how to reach
NIS — call 246-431-7400, visit the Frank Walcott Building, or nis.gov.bb — and
carries the salvaged reassurance panels (back-pay up to 3 years; "never too late").
For the `unsure` arrival it leads with "NISSS can check for you" and deliberately
omits the new-applicant form link, so an unsure user checks their status before
registering.

**Removed:** the `registration-guide` screen (orphaned once yes/no link out; its
useful content folded into `contact`), the register form, `screenConfirmation`,
`goToRegistrationGuide()`, `submitForm()`, `restart()` (only the deleted
confirmation used it), and the now-unused `firstName` / `contact` / `contactMethod`
state.

**Rewired other entry points:** next-steps "Have someone call me" → "Contact NISSS"
→ `contact`; tracker "Talk to an NISSS officer" → `contact`; payment-options
"Register now" → `register-path` (a genuine register action that routes to the
correct real form). Payment-options' Back button, which pointed at the removed
guide, was repointed to `next-steps`.

Net: 88 insertions, 300 deletions.

## Notable judgment calls

- **Contact page serves two arrivals.** Rather than two screens, one `contact`
  screen keys off `state.data.alreadyHasNis === 'unsure'` for its lead paragraph
  and Back target (`register-path` when unsure, else `next-steps`).
- **Question A left untouched.** Issue #31 also flags `payingNIS` ("Are you paying
  NIS now?") as collected-but-unused. The user's instructions covered only Question
  B and the contact page, so `payingNIS` is out of scope and flagged as a #31
  follow-up.
- **Phone number.** `check.html` uses 246-431-7400 while `how-to.html` uses
  467-4NIS (4647). Kept 246-431-7400 for consistency within `check.html`;
  reconciling the two pages is separate.

## Verification

No test harness exists for this single-file prototype, so verification was a
headless render pass (Node with a minimal DOM stub): all 12 screens in the
`SCREENS` map rendered with zero runtime errors, and the register-path Continue was
asserted per answer (yes → self-employment form, no → new-applicant form, unsure →
contact button, none → disabled). The `contact` screen was rendered for both the
`unsure` and non-unsure arrivals, confirming the Back target differs, the phone
number shows, and the new-applicant form is not linked. `grep` confirmed no
dangling `nav()` targets and no leftover references to the removed screens, state,
or functions. Both inline `<script>` blocks parse.
