# Desktop Migration Behavior Specification (Phase 0)

## Purpose
Capture observable behavior from the existing web application that must be preserved during the desktop rewrite.

## Canonical behavior areas

### 1) Authentication and profile lifecycle
- User can create a profile with required identity and credential fields.
- Credential confirmation must match before profile creation/update.
- Password strength indicator is advisory only (does not replace validation).
- Username/profile identifier is immutable after creation.

### 2) Monthly log semantics
- A month log contains day-scoped fields:
  - weight
  - trend (computed)
  - variance = weight - trend
  - exercise rung
  - flag
  - comments
- Trend and variance recalculate whenever a valid daily weight is entered or changed.
- Numeric display precision follows active unit/format settings.

### 3) Data-entry shorthand and operators
The following entry shortcuts must be preserved:
- `.` in a field copies previous nonblank value in that column.
- Abbreviated weight forms update portions of the prior weight according to existing shorthand rules.
- `+` in rung increments previous rung by one.
- `-` in rung decrements previous rung by one.

### 4) History and navigation
- User can browse months with existing data and open any month for review/edit.
- Navigation must preserve unsaved-change safeguards before context switches.

### 5) Chart and trend analytics
- Chart intervals include fixed presets (1 month, quarter, 6 months, year) and custom ranges.
- Trend analysis uses linear regression over selected interval.
- Reported outputs include at minimum:
  - rate of change over interval
  - inferred daily energy deficit/excess
  - min/mean/max trend values

### 6) Import/export
- CSV/XML import must validate structure and report row-level or field-level errors.
- Exported CSV/XML must preserve data needed to reconstruct logs and settings supported by format.
- Native backup/import path must keep source backup immutable.

### 7) Settings and localization
- Units: kg/lb/stones+lb display and conversion rules preserved.
- Energy units: kcal/kJ.
- Decimal separator and locale-aware number/date formatting preserved.
- Height formats support metric and imperial entry for BMI display.

## Validation and error behavior
- Invalid numeric/date input is rejected with inline, field-specific error state.
- Save action is non-destructive: failed validation must never partially write a record.
- Import errors produce actionable diagnostics and do not corrupt existing dataset.

## Phase 0 acceptance fixtures (initial)
These fixtures define expected outcomes and will become automated tests in later phases.

### Fixture A — Basic month entry and trend update
- **Given** an empty month log and known initial trend carry-forward.
- **When** day 1 and day 2 weights are entered with valid numeric values.
- **Then** trend values are computed for both days and variance equals `weight - trend` per day.

### Fixture B — Shorthand copy operator
- **Given** day N has rung `22` and comment `Travel`.
- **When** day N+1 rung and comment are entered as `.`.
- **Then** day N+1 values become `22` and `Travel`.

### Fixture C — Rung increment/decrement operators
- **Given** day N rung is `14`.
- **When** day N+1 rung is `+`.
- **Then** day N+1 rung is `15`.
- **When** day N+2 rung is `-`.
- **Then** day N+2 rung is `14`.

### Fixture D — Units and decimal formatting
- **Given** stored weight data and locale decimal separator `,`.
- **When** month log is displayed in kilograms then pounds.
- **Then** values render with locale separator and unit-appropriate precision.

### Fixture E — Import/export round-trip
- **Given** a representative CSV/XML dataset with at least one month of entries.
- **When** dataset is imported and then exported in same format.
- **Then** semantic equivalence holds for dates, weights, trends, rungs, flags, and comments.

## Out-of-scope for this specification revision
- UI widget-level visual style details.
- Cross-device cloud sync behavior.
- Multi-user network hosting mode.
