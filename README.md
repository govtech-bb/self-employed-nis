# NIS self-employed prototype — alpha draft for review

A prototype for **alpha.gov.bb** helping gig and self-employed workers in Barbados understand they can contribute to National Insurance (NIS), see what they'd get, and start. Built by GovTech Barbados with NIS.

**This is an alpha draft for feedback — not final, and not yet in the live site.** The benefit figures still need NIS sign-off (see "Open items").

## What's in here

Two information pages plus a calculator, on the alpha.gov.bb design system:

- **`index.html`** — **Start page.** *"Working for yourself? You can be covered."* Who it's for (with examples), what you get (the seven benefits), and two clear next steps: check your coverage, or see how to get covered.
- **`how-to.html`** — **Entry page.** *"How to get covered."* The practical steps: get your NIS number, register, top up if you have a main job, pay, check your benefits and record, and back-pay if you missed years.
- **`check.html`** — **Coverage calculator.** A short, mobile-first tool that shows, in dollars, what a few weeks off work would cost you and what NIS would pay — tailored to the kind of work you do. *(Figures are estimates pending NIS confirmation — see below.)*
- **`comments.js`** — the review-comment widget loaded by all three pages (see "Reviewing", below). Keep this file.

The pages link to each other: **Start ↔ How to get covered**, and Start → the calculator.

> Superseded: `what-you-get.html` and `not-too-late.html` are no longer used — their content was folded into the Start and Entry pages. Delete them from the repo.

## Reviewing

Open the GitHub Pages link (`https://govtech-bb.github.io/self-employed-nis/`). Please focus on **content and wording**, whether anything is **unclear or untrue**, and whether the journey **Start → How to get covered / Check your coverage** makes sense. It's mobile-first — worth checking on a phone.

**Leave feedback on the page:** select any text and a **💬 Comment** button appears. Use the **💬 Comments** panel (bottom-right) to read threads, reply, or resolve them. Comments are saved centrally (Supabase), so the whole team sees the same feedback. Setup notes are in `COMMENTS.md`.

## Known issues — expected, please ignore

These only resolve once the pages are inside the real alpha.gov.bb app:

- The **header logo**, **footer links**, and the **alpha banner link** point at live-site routes (`/`, `/services`, `/feedback`).
- The **"Register with NIS"** button works — it opens the real NIS registration portal in a new tab.

## Open items needing a decision

- **NIS fact-check (the main blocker).** Every benefit, amount, deadline and rule is sourced to NIS (Act 2023-25) in the source note on each page. The **calculator's dollar figures are unverified placeholders** — they sit in one clearly-marked block at the top of `check.html`'s script and must be confirmed by the NIS Self-Employed Unit before launch. The known conflict to resolve: the contextual brief says sickness benefit at the $1,200 minimum is ~$30/week; Ann-Marie's earlier prototype computed ~$133/week.
- **The opt-in is not collecting yet.** The calculator's "keep this summary" checkboxes are disabled, pending a signed-off privacy notice (no field collects data until then).
- **Currency style.** Amounts use `BDS$1,200.00` (two decimals). Confirm whether to keep the `.00` everywhere or drop it on round "about" figures.
- **Funeral grant** and a few benefit details are written plainly and general — confirm the exact names and conditions with NIS.
- **Bigger picture:** the PM's National Portable Benefits Framework (hours-tracking across employers, engager registration, portability) is **out of scope here** — this prototype is the voluntary self-employed funnel. Whether GovTech now scopes that platform is a Phase-2 decision.

## Notes for the developer

Standalone HTML for review. Each page carries a self-contained `<style>` block rebuilt from the live alpha.gov.bb design tokens (colours, spacing, type scale, Figtree) so it renders on its own with no build step.

**To bring into the site:** delete the `<style>` block, the Figtree font `<link>`s, and the `<script src="comments.js">` tag; lift the `<main>` content into the `(content)/[...slug]` route. The markup uses the real `govbb` utility classes, so the app's stylesheet and layout (official banner, yellow header, footer) take over. Restore the app's real header/nav. The calculator's estimate logic is isolated in one block in `check.html` for a clean swap when NIS confirms the numbers.

A few patterns aren't in the captured design-system markup and are flagged with `FLAG` comments — the **breadcrumb** and the calculator's option controls — confirm against a content-page template.

## Credits

Built by GovTech Barbados with NIS. The prototype code — the two content pages, the coverage calculator, the design-system styling, and the select-to-comment review widget — was produced with assistance from **Claude (Anthropic)**, directed and reviewed by the GovTech team.

---

*Plain Barbadian English. WCAG 2.2 AA (skip link, visible focus, breadcrumb landmark, `aria-current`, semantic headings, keyboard-operable calculator).*
