# Feature Parity Matrix (Web CGI → Desktop)

## Scope
This matrix maps the existing Hacker's Diet Online web workflows to the planned desktop application.

Legend:
- **P0**: must-have for first desktop release.
- **P1**: post-parity enhancement after functional equivalence.

## Matrix

| Capability area | Current web behavior | Desktop target behavior | Priority | Acceptance signals |
|---|---|---|---|---|
| Account creation & sign-in | User can create account, sign in, reset password flows; optional persistent login cookie. | Local profile creation and sign-in screen; optional profile lock PIN/password; no web cookie dependency. | P0 | User can create profile, close/reopen app, and sign back in to same data store. |
| Profile/settings management | Username immutable; editable password/email/name/height/units/decimal/public name options. | Settings screen preserving editable profile fields and units/format options; immutable profile identifier. | P0 | Changed settings persist and affect UI/formatting immediately or at next data refresh. |
| Monthly log entry | Daily weight, trend, variance, rung, flag, comments; shorthand entry operators (`.`, abbreviated weight, `+`/`-` rung). | Spreadsheet-style month grid preserving all fields and shorthand semantics with inline validation. | P0 | Editing a month reproduces the same computed trend/variance behavior for equivalent input data. |
| History navigation | Calendar-style month browser opens any stored month log. | History panel with month list/calendar and one-click open in editor. | P0 | User can navigate to arbitrary historical month and edit/save. |
| Chart workshop | Predefined periods + custom date range charting; trend overlays and optional series. | Chart view with same period presets, custom range, and equivalent plot toggles. | P0 | Chart point counts/date ranges/series visibility match expected period definitions. |
| Trend analysis | Linear-regression-based trend intervals, min/avg/max trend, implied energy balance. | Trend analytics screen with same interval calculations and summaries. | P0 | Statistical outputs are within documented numerical tolerance vs baseline fixtures. |
| Utilities (diet calc, paper forms, recalc) | Additional utility actions from dedicated utilities section. | Utilities hub with parity for core utilities; print/export features integrated with desktop share/print UX. | P1 | At least diet planning and trend recalc workflows available with test coverage. |
| Import/export | CSV/XML import and export; native backup download. | Import/export wizard for CSV/XML and native backup archive. | P0 | Round-trip import→export preserves semantic data equivalence for fixture datasets. |
| Public account browsing / feedback | Web-hosted account discovery and feedback form to service operator. | Reframed for offline desktop: local help/contact links and optional exportable diagnostic bundle. | P1 | Desktop substitutes do not block core self-tracking workflows. |
| Accessibility/display adaptation | Handheld mode and browser formatting variants. | Desktop responsive layout, scalable typography, keyboard-first entry, high-contrast support. | P0 | Core workflows are fully keyboard-operable and readable at common DPI scales. |

## Notes for implementation sequencing
1. Prioritize parity for: profile, monthly log, history, chart, trend, settings, and import/export.
2. Treat online-service-specific functions as deferred unless a local equivalent is clearly defined.
3. Preserve calculation semantics first, then improve interaction design.
