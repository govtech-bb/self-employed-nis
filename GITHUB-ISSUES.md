# GitHub issues — now filed on the repo

The open items from the alpha review of the three self-employed NIS pages are now
**filed as GitHub issues** on this repo (Issues was previously disabled; it's now on).

- **Epic / tracking issue:** [#18 — Self-employed NIS pages — alpha review & tidy-up follow-ups](https://github.com/govtech-bb/self-employed-nis/issues/18) — links every item below and shows progress as they close.
- Live preview: <https://govtech-bb.github.io/self-employed-nis/> (Start), plus `how-to.html` and `check.html`.

The list combines the original content/NISSS review items with the production-readiness
**tidy-up** carried over from the earlier prototype (accessibility, em-dashes, external-link
cues, input validation).

## Tidy-up (accessibility + polish)

| # | Item |
|---|------|
| [#1](https://github.com/govtech-bb/self-employed-nis/issues/1) | Remove em-dashes from user-facing copy (67 across the pages) |
| [#2](https://github.com/govtech-bb/self-employed-nis/issues/2) | Accessibility pass to WCAG 2.2 AA (axe: contrast on check footer links, landmarks; + manual keyboard/SR pass) |
| [#3](https://github.com/govtech-bb/self-employed-nis/issues/3) | Add an "opens in a new tab" cue to the Register with NIS button (footer links are platform chrome, aligned at migration) |
| [#4](https://github.com/govtech-bb/self-employed-nis/issues/4) | Verify the calculator's number inputs clamp the visible field |

## Content / NISSS review

| # | Item | Blocked on |
|---|------|-----------|
| [#5](https://github.com/govtech-bb/self-employed-nis/issues/5) | **NIS fact-check: calculator figures + benefit amounts (LAUNCH BLOCKER)** | NISSS |
| [#6](https://github.com/govtech-bb/self-employed-nis/issues/6) | Opt-in data collection pending a signed-off privacy notice | privacy/NISSS |
| [#7](https://github.com/govtech-bb/self-employed-nis/issues/7) | Confirm "what to bring" / ID requirements | NISSS |
| [#8](https://github.com/govtech-bb/self-employed-nis/issues/8) | State the yearly contribution cap | NISSS |
| [#9](https://github.com/govtech-bb/self-employed-nis/issues/9) | Add qualifying periods for each benefit | NISSS |
| [#10](https://github.com/govtech-bb/self-employed-nis/issues/10) | Make "what drives your benefits" consistent | NISSS |
| [#11](https://github.com/govtech-bb/self-employed-nis/issues/11) | Add a path for first-time workers with no NI number | NISSS |
| [#12](https://github.com/govtech-bb/self-employed-nis/issues/12) | Clarify whether the coverage check is anonymous | product/NISSS |
| [#13](https://github.com/govtech-bb/self-employed-nis/issues/13) | Align back-payment surcharge wording | content (no new facts) |
| [#14](https://github.com/govtech-bb/self-employed-nis/issues/14) | Add pension-age figures where pension is discussed | content |
| [#15](https://github.com/govtech-bb/self-employed-nis/issues/15) | Decide currency house style (decimals in headings vs body) | content/design |
| [#16](https://github.com/govtech-bb/self-employed-nis/issues/16) | Confirm survivors' benefit eligibility wording | NISSS |
| [#17](https://github.com/govtech-bb/self-employed-nis/issues/17) | Restore a low-commitment cue near the register CTA | content |

## Reconciled since the first draft of this file

- **"Build the coverage check tool (/check)" — done.** `check.html` now exists, so that item was dropped.
- **Deleted pages removed from links.** `what-you-get.html` and `not-too-late.html` were folded into `index.html` / `how-to.html` and deleted; their content items (qualifying periods, benefits-consistency, surcharge, pension age, survivors) were remapped to the current pages.
- **"Flag links that open in a new tab"** (old item #13) merged into #3 above.
- Preview link corrected to the `govtech-bb.github.io` host.

*Already done (not tracked): register link to the real NIS portal, dead buttons removed, breadcrumbs and wording fixes.*
