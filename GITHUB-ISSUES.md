# GitHub issues — parent + sub-issues

Open items from the review of the NISSS self-employed content pages, structured as one **parent (tracking) issue** with each item as a **sub-issue** under it.

## How to add them as sub-issues

1. Create the **parent issue** below (copy its title and body).
2. Open that issue. Find the **Sub-issues** section (under the description, or **Create sub-issue** in the right sidebar) → **Create sub-issue**.
3. Paste one sub-issue's title and body, add its labels, create. Repeat for each.

GitHub shows a progress bar on the parent as sub-issues are closed. Most sub-issues need **NISSS** to confirm a fact or supply a URL, so tag your NISSS contact on those.

*(Don't want to paste each one by hand? Connect a GitHub tool and I can file the parent and all sub-issues for you — just say the word.)*

---

# PARENT ISSUE

## Title: Self-employed NI content pages — alpha review follow-ups
Tracking issue for open items from the service-design / content review of the three self-employed National Insurance pages.

Live preview:
- [Landing](https://amogendukwe-sd.github.io/self-employed-nis/)
- [What you get](https://amogendukwe-sd.github.io/self-employed-nis/what-you-get.html)
- [It's not too late](https://amogendukwe-sd.github.io/self-employed-nis/not-too-late.html)

Sub-issues are listed below this one. The main blocker before production is the NISSS fact-check.
**Labels:** `epic`, `alpha`

---

# SUB-ISSUES

**What's left to fix (13 open):**

1. Build the coverage check tool (/check)
2. Confirm "what to bring" / ID requirements with NISSS
3. State the yearly contribution cap
4. Add qualifying periods for each benefit
5. Make "what drives your benefits" consistent across the benefits page
6. Add a path for first-time workers with no NI number
7. Clarify what the coverage check does and whether it's anonymous
8. Align back-payment surcharge wording across pages
9. Add pension-age figures on "It's not too late"
10. Decide currency house style (decimals in headings vs body)
11. Confirm survivors' benefit eligibility wording
12. Restore a low-commitment cue near the register CTA
13. Accessibility — flag links that open in a new tab

*Of these, #8 and #9 are content-only (no new facts) and could be done now; the rest need a NISSS confirmation or a product/design decision. Already done (not listed): register link added, both dead buttons removed, breadcrumbs and wording fixes — see REVIEW-NOTES.md.*

---

## Title: Build the coverage check tool (/check)
The landing and benefits pages now say a coverage check is "coming soon" (the dead `/check` button was removed). Build the tool — a short calculator that helps a self-employed worker work out the right amount to contribute — and replace the "coming soon" notes with a link to it.
**Needs:** product/dev. **Labels:** `feature`, `alpha`

## Title: Confirm "what to bring" / ID requirements with NISSS
The landing page says *"Bring your photo ID — a trader's card is enough if you don't have a birth certificate to hand"* (mixes photo ID, trader's card, and birth certificate confusingly). *It's not too late* just says *"visit any office with photo ID."* Agree one clear, correct "what to bring" statement and use it identically on both pages.
**Needs:** NISSS to confirm actual requirements. **Labels:** `content`, `needs-NISSS`

## Title: State the yearly contribution cap
The benefits page says *"the more you pay in, up to the yearly cap…"* but never gives the cap amount. Add the figure, or remove the reference.
**Needs:** NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Add qualifying periods for each benefit
On the benefits page, a qualifying period (*"You qualify after a year of contributing"*) is given only for Sickness benefit. Maternity/paternity, invalidity, survivors', and old-age pension say nothing, so users can't tell if/when they qualify. Add the qualifying period for each, or one line explaining how they work.
**Needs:** NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Make "what drives your benefits" consistent across the benefits page
The benefits page currently gives three different signals about what determines a person's benefits, which read as contradictory:
- *Important* box: "The more you pay in, up to the yearly cap, the more you get back" (amount-based)
- *Old age pension*: "Pension is worked out from your best 10 years of earnings, **not the amount you pay in**" (earnings-based — explicitly not amount)
- *Tip from NISSS*: "The number of contributions is what matters"; and *Sickness benefit*: "You qualify after a year of contributing" (count-based)
Different benefits may genuinely use different bases (e.g. pension on earnings, eligibility on contribution count), but as written a user can't tell what to do. **Action:** confirm with NISSS what each benefit is based on, then make the page consistent — e.g. clearly separate what makes you *eligible* (number of contributions) from what sets the *amount* (earnings / how much you pay).
**Needs:** NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Add a path for first-time workers with no NI number
All three pages assume the reader already has a National Insurance number (*"Your National Insurance number is all you need"*). Add a short "Don't have an NI number yet?" route/line for first-time workers (likely the youngest gig users).
**Needs:** NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Clarify what the coverage check does and whether it's anonymous
Landing copy implies the coverage check collects personal details (*"We won't ask for your name or any details unless you choose…"* — wording now changed, but the question stands for when the tool ships). Decide and state plainly whether the check is anonymous, given the privacy reassurance elsewhere on the page.
**Needs:** product + NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Align back-payment surcharge wording across pages
Benefits page: *"NISSS adds 5% for each year you missed, up to three years"* can be misread as a flat 5% capped at three years. *It's not too late* explains it clearly with a worked example (5% / 15%). Reword the benefits page to match the per-year framing. (Safe to do — the fact is already on the other page.)
**Needs:** content edit (no new facts). **Labels:** `content`, `good-first-issue`

## Title: Add pension-age figures on "It's not too late"
That page refers to *"pension age"* but the actual ages (67, or reduced from 60) only appear on the benefits page. Add the ages, or link to the benefits page at that point.
**Needs:** content edit. **Labels:** `content`

## Title: Decide currency house style (decimals in headings vs body)
Amounts use `BDS$1,200.00` everywhere per the two-decimal data rule. The `.00` reads heavy in headings ("What you get for BDS$1,200.00 a year") and slightly odd next to estimates ("about BDS$23.00 a week"). Decide a house style — e.g. drop decimals in headings, keep in body.
**Needs:** content/design decision. **Labels:** `content`

## Title: Confirm survivors' benefit eligibility wording
Benefits page: *"A payment to your husband, wife, or children if you pass away while contributing."* "while contributing" may imply you must be actively paying at time of death. Confirm the real rule with NISSS and reword if broader.
**Needs:** NISSS. **Labels:** `content`, `needs-NISSS`

## Title: Restore a low-commitment cue near the register CTA
The original calculator button said "in 3 minutes," which set a useful low-effort expectation. Consider a short reassurance near the "Register with NISSS" button or the coverage-check note (e.g. "Takes a few minutes").
**Needs:** content; confirm timing with NISSS. **Labels:** `content`, `enhancement`

## Title: Accessibility — flag links that open in a new tab
The "Register with NISSS" button and the footer "Careers" link open in a new tab (`target="_blank"`). For WCAG, add a visible or screen-reader cue that the link opens in a new tab/leaves the site. Match whatever pattern the main alpha.gov.bb site uses.
**Needs:** design-system decision. **Labels:** `accessibility`
