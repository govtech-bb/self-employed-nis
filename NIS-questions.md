# Questions for NIS (the MDA)

Organised so the questions that unblock the calculator come first. Drawn from the
"Questions for MDA" in the meeting notes, the gaps flagged in the contextual brief,
and the placeholder figures in `check.html`.

Each question has:
- **Refers to:** what the question is actually about.
- A **status** tag (see key).
- An **Answer:** line — pre-filled where a document already gives it; tick the box once NIS confirms.

**Status key:** ✅ answered in the documents (just confirm) · 🟡 partly answered, needs NIS to confirm the exact number/rule · 🔴 no answer anywhere, NIS must provide · ⚪ internal decision (not an NIS question)

---

## 1. Benefit amounts — the calculator can't go live without these (the main blocker)

- [ ] 🟡 **1. Sickness benefit formula.** At the $1,200/year minimum, is the weekly sickness benefit **~$30** or **~$133**?
  - Refers to: how much a person gets per week if they're off sick, at the lowest contribution level. This is the single number blocking the calculator.
  - Answer (from brief §2.2): $1,200/yr implies a ~$46/week insurable wage → **~$30/week**. The $133 was a higher contribution level. *Confirm the exact formula.*
- [ ] 🟡 **2.** What **% of insurable income** does each benefit replace, and is there a floor and ceiling?
  - Refers to: the replacement rate — e.g. sickness pays X% of your insurable earnings, up to a max.
  - Answer: Brief implies ~65% for sickness (~$30 on ~$46), but the official rate/floor/ceiling isn't stated.
- [ ] 🔴 **3. Maternity benefit** — total entitlement for a qualifying event.
  - Refers to: how much, lump sum and/or weekly, and for how many weeks, when someone has a baby.
  - Answer:
- [ ] 🔴 **4. Paternity benefit** — amount and duration.
  - Refers to: paid time off / payment for new fathers.
  - Answer:
- [ ] 🔴 **5. Invalidity benefit** — how is it calculated and what's the minimum?
  - Refers to: ongoing payment if a long-term illness or injury stops someone working.
  - Answer:
- [ ] 🔴 **6. Survivors' benefit** — who qualifies (spouse, children) and how much?
  - Refers to: what a person's family receives if they die.
  - Answer:
- [ ] 🔴 **7. Funeral grant** — exact amount and who can claim it.
  - Refers to: the one-off payment toward funeral costs.
  - Answer:
- [ ] 🟡 **8. Old age pension** — confirm best-10-years basis and the minimum contributory pension floor.
  - Refers to: the monthly income at retirement, and the smallest pension someone can get.
  - Answer: Brief confirms a **minimum contributory pension floor exists** (§2.2), but not the dollar amount or the best-10-years rule.

## 2. Eligibility & contribution rules

- [ ] 🟡 **9. Qualifying period** — does cover begin after one full year of contributions? Different per benefit?
  - Refers to: how long someone must pay before they're actually covered.
  - Answer: We've assumed **one full year**; flagged in the README as needing NIS sign-off.
- [ ] 🟡 **10.** Is entitlement based on the **number of contributions** or the **amount** paid — or both?
  - Refers to: whether what you get depends on how often you paid, how much, or both.
  - Answer: Brief refers to contribution *weeks* credited; interplay with contribution *amount* for benefit level isn't spelled out.
- [ ] 🔴 **11. Youngest and oldest age** a self-employed person can start contributing.
  - Refers to: age limits for joining and for paying in.
  - Answer:
- [ ] 🔴 **12. Dormancy** — what happens to a record if someone stops contributing for a while?
  - Refers to: whether a person loses anything (credits, entitlement) if they pause payments.
  - Answer:
- [ ] ✅ **13. Adequacy target** — is there a recommended contribution relative to income?
  - Refers to: an "aim for this much" figure, since the $1,200 minimum may not be enough cover.
  - Answer (from brief §2.2): Yes — e.g. someone earning $3,000/month should aim for **~$4,140/yr**, not the $1,200 minimum. *Decide whether to surface this in the tool.*

## 3. Back-pay, deadlines & penalties

- [ ] ✅ **14.** Confirm **back-pay up to 3 years** and the **5% surcharge** — per missed year, and on what base?
  - Refers to: catching up on missed years and the late fee that applies.
  - Answer (from brief data model): **Up to 3 missed years, 5% surcharge per missed year.** *Confirm the calculation base.*
- [ ] ✅ **15. Annual contribution deadline.** Confirm the **15 January** cut-off.
  - Refers to: the date a self-employed person's contributions for a year must be paid in by. This is what triggers the 5% surcharge (#14) if missed.
  - Answer (from brief §1.2): The minimum $1,200/yr is **payable by 15 January of the succeeding year**, as a single payment or instalments. (So 2026's contributions are due by 15 Jan 2027.) *Confirm it applies to all contributors with no sector exceptions.*
- [ ] 🟡 **16.** Any penalty for a **missed or skipped month**?
  - Refers to: whether someone is penalised for not paying in a given month (we tell users there's no penalty).
  - Answer: The voluntary/instalment model implies no per-month penalty (surcharge applies to missed *years*), but it's not stated outright.

## 4. Registration, payment & data (backs up our "it's not a tax" claim)

- [ ] 🟡 **17. NIS↔BRA data sharing** — is any contribution data shared with BRA or used for tax enforcement?
  - Refers to: the reassurance on the landing page that NIS info isn't shared with the tax authority or used to chase people.
  - Answer: **Ann-Marie was to verify** — not yet confirmed. Underpins the "not a tax" reassurance, so we need a definitive yes/no.
- [ ] ✅ **18. Registration channels & documents.**
  - Refers to: how people sign up and what they need to bring.
  - Answer (meeting notes): Online (nis.gov.bb), phone (Self-Employed Unit), in person; minimal docs (birth certificate / trading card).
- [ ] ✅ **19. Payment channels.**
  - Refers to: how people pay, and whether small regular payments are possible now.
  - Answer (brief §3.2 / §5.4): Instalments and electronic payment already permitted (Reg 4A); EZpay+ is live. *Confirm SurePay / CIBC First Pay / office options.*

## 5. Wording & scope to confirm

- [ ] 🟡 **20. Benefit names** — confirm exact official names and conditions.
  - Refers to: making sure we use NIS's official terms (e.g. "funeral grant" vs the official name).
  - Answer: Mostly known; README flags the funeral grant name/conditions for confirmation.
- [ ] ⚪ **21. Currency style** — `BDS$1,200.00` with `.00`, or drop it on round figures?
  - Refers to: how money is displayed across the pages. **Internal GovTech/design decision — not an NIS question.**
  - Answer:
- [ ] ✅ **22. Portable Benefits / Moore Resolution** — in scope now, or Phase 2?
  - Refers to: the bigger "benefits follow the worker" platform (engager registration, hours tracking, portability).
  - Answer (brief §6): **Design the data layer in now, activate later.** Whether GovTech scopes the full platform now is a Phase-2 decision.

---

**Highest priority:** the maternity / paternity / invalidity / survivors' / funeral amounts (**#3–7**) plus the sickness formula (**#1**) — these are the biggest blanks for the calculator. Every dollar figure in `check.html` is an unverified placeholder until NIS confirms them.
