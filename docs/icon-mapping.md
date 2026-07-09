# Icon mapping — wizard `ICONS` → Lucide

The wizard (`check.html`) renders all its icons from a single `ICONS` map. Every
glyph is sourced from [Lucide](https://lucide.dev) (ISC licence). Icons are
decorative: each SVG carries `aria-hidden="true"` and `focusable="false"`, and
every icon sits next to a text label — there are no icon-only controls.

This table is the source of truth for the React port. `@govtech-bb/react` ships a
generic `<Icon>` wrapper but no named icon library, so at port time each `ICONS`
key maps to the Lucide name below (via `lucide-react` or equivalent). The port is a
lookup, not a re-decision.

## Worker types

| `ICONS` key | Concept | Lucide |
|---|---|---|
| `freelancer` | Freelance / professional (used by both worker types) | `laptop` |
| `delivery` | Drive or deliver, rideshare | `truck` |
| `creative` | Design / arts | `palette` |
| `vendor` | Market stall, sell goods | `store` |
| `tradesperson` | Trades / construction | `wrench` |
| `domestic` | Domestic / care work | `house` |
| `other` | Miscellaneous / rental (Airbnb) | `ellipsis` |
| `agri` | Agriculture | `sprout` |
| `beauty` | Beauty services | `sparkles` |

## Benefits

| `ICONS` key | Concept | Lucide |
|---|---|---|
| `sickness` | Sickness benefit | `heart-pulse` |
| `maternity` | Maternity benefit | `baby` |
| `paternity` | Paternity benefit (semi-related — family unit) | `users-round` |
| `invalidity` | Invalidity / disability benefit | `accessibility` |
| `survivors` | Survivors' / dependants' benefit (semi-related — support) | `heart-handshake` |
| `pension` | Retirement pension | `piggy-bank` |

`maternity`, `paternity` and `survivors` have no literal Lucide glyph. They were
chosen to stay visually distinct from one another at rendered size (28px): an infant
(`baby`), a group of people (`users-round`), and a hand-and-heart (`heart-handshake`).

## Utility

| `ICONS` key | Lucide | | `ICONS` key | Lucide |
|---|---|---|---|---|
| `check` | `check` | | `phone` | `phone` |
| `arrowLeft` | `arrow-left` | | `building` | `building` |
| `arrowRight` | `arrow-right` | | `globe` | `globe` |
| `shield` | `shield` | | `card` | `credit-card` |
| `lock` | `lock` | | `chart` | `chart-line` |
| `chevDown` | `chevron-down` | | `download` | `download` |
| `warning` | `triangle-alert` | | `bell` | `bell` |
| `spark` | `sparkle` | | `circleCheck` | `circle-check` |
| `clock` | `clock` | | `circleMinus` | `circle-minus` |
| `doc` | `file-text` | | `store` | `store` |
| `calendar` | `calendar` | | `bolt` | `zap` |

## Notes

- **Removed:** the previous `taxi` (folded into `delivery`) and `heart` (unused) keys.
- **Style:** pure stroke throughout. The earlier hand-drawn `sickness`/`pension`
  used a tinted fill (`opacity=".12"`); that treatment was dropped so every icon
  shares one Lucide stroke style. Size classes (`w-7 h-7` cards, `w-6 h-6`/`w-5 h-5`
  chrome) and per-icon `stroke-width` were preserved from the original design.
- `index.html`, `how-to.html` and `landing-page-how-to.html` contain only page chrome
  SVGs (logo / coat of arms), not content-icon lists, so there was nothing to convert
  there.
