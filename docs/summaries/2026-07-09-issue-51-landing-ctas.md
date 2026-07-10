# Landing CTA swap (issue #51)

**Why green as a variant class:** `.btn-primary--green` overrides only the
background states and rides on `.btn-primary` for everything else (layout,
focus ring, aria-disabled machinery), so the a11y behaviour from the WCAG pass
is inherited, not duplicated. Hover `#1a7a5e` / active `#004634` follow the
teal base's lighten/darken pattern; both keep white text above 4.5:1. The
focus ring deliberately stays app-wide teal for consistency.

**Why step 1, not the plan screen:** the estimate is only personal if it is
built from the user's own answers; the old deep-link landed on step 5 with
default figures (and created a wrong-back path, recorded in #30 - one of its
two confirmed repros disappears with this change).

**Kept:** the lower "Learn more about each one" link to the same benefits page
(owner decision - it reads naturally in the benefits-list context).

**Flagged, out of scope:** benefits-quick has its own "Estimate my
contributions" secondary that still deep-links to the plan screen - same
original problem, one screen over. Noted on the PR as a follow-up candidate.

**Verified headlessly:** green renders rgb(0,101,74); primary first; estimate
click lands on "What kind of work do you do?" with "Step 1 of 6"; benefits
click lands on "Six ways NISSS protects you."; Back returns to landing from
both; no nav('plan') in screenLanding; axe zero violations; zero page errors.
