# NISSS self-employed content pages — alpha draft for review

Three content pages for **alpha.gov.bb**, helping gig and self-employed workers in Barbados understand they can contribute to National Insurance. Built with NISSS (National Insurance and Social Security Service).

**This is an alpha draft for feedback — not final, and not yet in the live site.** The facts still need NISSS sign-off (see "Open items" below).

## The pages

- **`index.html`** — *"Working for yourself in Barbados? You can be covered."* The landing page. Answers "does this apply to me?"
- **`what-you-get.html`** — *"What you get for BDS$1,200.00 a year."* The benefits page.
- **`not-too-late.html`** — *"It's not too late to start."* For over-50s and people who stopped contributing.

## How to review

Open the GitHub Pages link for this repo (Settings → Pages once it's enabled). The landing page is the front page; use the **"See also"** links at the bottom of each page to move between the three.

Please focus on: **the page content and wording**, whether anything is **unclear or untrue**, and whether the journey between the three pages makes sense. The pages are mobile-first — worth checking on a phone.

## Known issues — expected, please ignore for now

These only resolve once the pages are inside the real alpha.gov.bb app, so they'll break on the review link:

- The **header logo**, **footer links** (Home / Terms / Careers), and the **alpha banner link** point at live-site routes (`/`, `/services`, `/feedback`, etc.).
- The green **"Check your cover in 3 minutes"** button points at `/check` — the cover-check calculator, to be built later.
- **"How to ask for your record"** (on the *not-too-late* page) has no destination yet.

## Open items needing a decision

- **NISSS fact-check.** Every benefit, amount, deadline, and rule is sourced to NISSS (Act 2023-25, May 2026) in the source note on each page. NISSS needs to confirm these before the pages go live.
- **Currency style.** Amounts use `BDS$1,200.00` per the Barbados data-format rule (always two decimals). The `.00` reads a little heavy in the headline "What you get for BDS$1,200.00 a year" — confirm whether to keep it or show `BDS$1,200` in headings only.
- **Destinations** for the two placeholder links above.

## Notes for the developer

These are standalone HTML files for review. Each one carries a self-contained `<style>` block built from the live alpha.gov.bb design tokens (exact colours, spacing, type scale, Figtree font) so it renders on its own with no build step.

**To bring into the site:** delete the `<style>` block and the two Figtree font `<link>`s, and lift the `<main>` content into the `(content)/[...slug]` route. The markup already uses the real `govbb` Tailwind utility classes, so the app's own stylesheet and layout (official banner, yellow header, footer) take over. Restore the app's real header/nav (the standalone files show the nav links at all widths because the JS hamburger needs React).

A few patterns aren't in the captured design-system markup and are flagged with `FLAG` comments in the HTML — the **breadcrumb**, the **accordion** (native `<details>`), and the **sector cards** — confirm these against a content-page template.

---

*Plain Barbadian English, WCAG 2.2 AA (skip link, visible focus, breadcrumb landmark, `aria-current`, semantic headings). Built by GovTech Barbados with NISSS.*
