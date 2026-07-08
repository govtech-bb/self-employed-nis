# 0001 — The interactive tool collects no data and simulates no submission

**Status:** Accepted · **Date:** 2026-07-08
**Relates to:** issue #8 (interactive-tool end state), issue #31 (register-path routing)

## Context

The self-employed NIS tool (`check.html`) is a guidance and estimator wizard, not
a registration system. An earlier design ended the journey with a "Tell us where
to reach you" form that collected a name, a phone number or email, and a preferred
contact method, then showed a confirmation screen with an invented reference
number ("someone from NISSS will reach out within 5 working days").

That end state is dishonest for a frontend prototype with no backend: it collects
personal data it cannot protect or act on, and it fabricates a submission and a
reference that do not exist. It also duplicates work NIS already does on its own
registration forms.

## Decision

The tool is a **signposting frontend only**. Concretely:

- It **collects no personal data** (no name, contact details, or contact-method
  capture) and stores nothing that identifies a user.
- It **never simulates a submission** and never issues a confirmation screen or a
  fabricated reference number.
- **Real registration is delegated to NIS's own forms** — the tool links out to
  the live `nis.gov.bb` registration pages rather than rebuilding registration.
- Any "get help" or "not sure" step gives the user **contact channels to initiate
  themselves** (call, visit an office, the NIS website), rather than promising that
  NIS will reach out.

## Consequences

- Future screens must not reintroduce PII-collecting form fields or a fake
  submit/confirmation flow. A "contact" step is static contact information, not a
  data-capture form.
- When a step needs the user to register or pay, it links out to the real NIS
  service rather than emulating it in-tool.
- This supersedes the "have NISSS contact you" form pattern, which has been removed
  along with its `submitForm()`, `confirmation` screen, and `firstName` / `contact`
  / `contactMethod` state.
