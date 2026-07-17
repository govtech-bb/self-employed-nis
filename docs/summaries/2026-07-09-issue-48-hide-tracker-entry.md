# Hide the landing tracker entry (issue #48)

**Why a flag, not a comment-out or deletion:** the block lives inside
`screenLanding()`'s template literal, where an HTML comment would still ship
markup to the DOM and a deletion would fight the issue's "easy to restore"
requirement. A single `SHOW_TRACKER_ENTRY = false` const (defined beside the
other top-level constants, with the rationale in a comment) guards the block
via `${!SHOW_TRACKER_ENTRY ? '' : `...`}` — flipping one boolean restores the
section verbatim.

**Verified:** the section and card are gone from the landing page; no control
on any of the 12 screens navigates to the tracker; `screenTracker` +
`SCREENS`/`TITLES` entries intact and the screen still renders when navigated
programmatically; zero page errors.
