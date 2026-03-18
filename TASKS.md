# Migration Plan: Web CGI App → Modern C/C++ Desktop Application

## Goal
Rebuild **The Hacker's Diet Online** from a Perl CGI + JavaScript web stack into a modern desktop application written in C or C++, while preserving core functionality (account/session flows, monthly logs, trend analysis, charting, import/export, settings, and history) and improving UI/UX.

## Repository Analysis Summary

### Current architecture and technology
- The project is currently a **web application** implemented as a **Perl CGI program** with browser-side JavaScript enhancements.
- The source of truth for the app logic is the literate-programming source (`src/hdiet.w`), which generates `HackDiet.pl` and modules in `src/HDiet/`.
- Data persistence is file-based (flat files in the Unix file system), not a relational database.
- The documentation/UI assets in `webdoc/` and CSS/JS files capture existing workflows and functional expectations.

### Feature inventory to carry forward
From repository structure and docs, the desktop rewrite must preserve these capability areas:
1. User/account management and sign-in flows.
2. Monthly weight/exercise log editing.
3. Trend fitting and historical analytics.
4. Charting and visual summaries.
5. Data import/export (CSV/XML and compatibility-oriented formats).
6. Preferences/settings (units, locale-related formatting, etc.).

### Key constraints and risks
- Existing logic is spread across Perl modules and a large literate source, so behavior extraction must be methodical.
- The current file-based model is simple but may not scale for desktop concurrency/sync; storage format decisions affect migration and backward compatibility.
- UI behavior is currently web-form-centric and must be redesigned, not merely replicated, to achieve modern desktop UX.

---

## Proposed Target Architecture (C/C++)

### Recommended stack
- **Language**: C++20 (preferred over C for maintainability and UI framework ecosystem).
- **UI framework**: **Qt 6** (Widgets + Qt Charts, or QML + Qt Quick Controls 2 for richer UX).
- **Persistence**: **SQLite** as canonical local store.
- **Import/Export layer**: CSV/XML adapters for backward compatibility with historical data interchange.
- **Charting**: Qt Charts (or QCustomPlot if finer control is needed).
- **Packaging**: CMake + CPack; platform-specific installers (MSI/DMG/AppImage).

### High-level module decomposition
1. `core/`
   - domain models (UserProfile, DayEntry, MonthLog, TrendSeries, Settings)
   - business rules (trend calculation, variance, energy balance math, unit conversion)
2. `storage/`
   - SQLite schema + repository layer
   - migration and backup manager
3. `interop/`
   - CSV/XML importer/exporter
   - legacy parser/adapters for original formats
4. `ui/`
   - sign-in/welcome
   - dashboard
   - monthly log editor
   - trend/history analysis
   - settings/preferences
5. `app/`
   - startup, dependency wiring, logging, crash reporting hooks

---

## Detailed Execution Plan

## Phase 0 — Discovery and specification freeze
- [ ] Build a **feature parity matrix** mapping current behavior to target desktop screens.
- [ ] Inventory all user-visible forms, validations, and calculations from Perl/JS and `webdoc/hdo.html`.
- [ ] Define explicit non-goals for v1 desktop (if needed), e.g., no server-hosted multi-user mode.
- [ ] Produce acceptance criteria per feature with sample input/output fixtures.

**Deliverables**
- `docs/migration/feature-parity.md`
- `docs/migration/behavior-spec.md`
- `docs/migration/non-goals.md`

## Phase 1 — Project bootstrap
- [ ] Initialize CMake-based monorepo layout for app/core/storage/interop/tests.
- [ ] Add dependency management strategy (vcpkg or Conan) and lock baseline versions.
- [ ] Configure CI for Linux/Windows/macOS builds with unit tests.
- [ ] Establish coding conventions and architecture decision records (ADRs).

**Deliverables**
- `CMakeLists.txt` + module-level CMake files
- CI pipelines (GitHub Actions)
- `docs/adr/` initial decisions

## Phase 2 — Domain and calculation engine port
- [ ] Re-implement trend-fitting and metric calculations from Perl modules (`trendfit.pm`, month/history logic).
- [ ] Port unit conversion and locale-safe numeric/date handling.
- [ ] Create deterministic test vectors from current Perl outputs.
- [ ] Ensure algorithmic parity with tolerances documented.

**Deliverables**
- `src/core/*`
- `tests/core/*` with golden tests referencing Perl baseline fixtures

## Phase 3 — Storage redesign and migration path
- [ ] Design SQLite schema for users, logs, settings, and computed caches.
- [ ] Implement repository layer and transaction boundaries.
- [ ] Add import path from legacy flat-file/CSV/XML artifacts.
- [ ] Implement backup/restore and schema migration versioning.

**Deliverables**
- `src/storage/*`
- `docs/storage/schema.md`
- migration tool and tests

## Phase 4 — Modern UI/UX implementation
- [ ] Create information architecture and navigation model (dashboard-first UX).
- [ ] Implement screens:
  - Welcome/sign-in/profile setup
  - Monthly log grid editor with keyboard-first data entry
  - Trend/history visual analytics view
  - Settings/preferences
- [ ] Add modern UX patterns:
  - progressive disclosure
  - inline validation and helpful error states
  - undo/redo for edits
  - autosave and clear save-state indicators
  - accessible color contrast and scalable typography
- [ ] Introduce responsive desktop layouts for common resolutions and HiDPI.

**Deliverables**
- `src/ui/*`
- UX spec and interaction flows in `docs/ux/`

## Phase 5 — Interoperability and data trust
- [ ] Implement CSV/XML import/export compatible with existing workflows.
- [ ] Validate round-trip fidelity across representative datasets.
- [ ] Provide conflict-resolution UI for malformed/partial imports.

**Deliverables**
- `src/interop/*`
- `tests/interop/*`
- user-facing import/export guide

## Phase 6 — Quality, security, and release readiness
- [ ] Add comprehensive automated tests (unit/integration/UI smoke).
- [ ] Add static analysis (clang-tidy, cppcheck) and sanitizers in CI.
- [ ] Threat model local data handling (credential storage, file permissions, secrets at rest).
- [ ] Finalize installers, code signing strategy, telemetry policy (if any), and release notes.

**Deliverables**
- release candidate builds
- QA checklist and sign-off report

---

## Work Breakdown by Functional Area

### A. Authentication and profile
- Replace CGI/session-cookie mechanics with local desktop identity model.
- Decide whether app supports:
  1) single local profile only (simpler), or
  2) multiple local profiles with lock PIN/password.
- Store sensitive values via OS keychain where feasible.

### B. Monthly log editing
- Provide spreadsheet-like editor with fast row/column navigation.
- Implement smart defaults (carry-forward trend, unit-aware parsing).
- Add explicit dirty-state/unsaved-change protection.

### C. Trend and history analytics
- Preserve original trend semantics first, then add optional enhanced visualizations.
- Include overlays (weight vs trend vs variance) and period filters.
- Export chart image/PDF from desktop.

### D. Settings and personalization
- Preserve unit options and decimal separator behavior.
- Add theme support (light/dark) and accessibility settings.

### E. Data migration
- Offer first-run migration wizard to import legacy exports.
- Keep immutable backup before conversion.
- Provide migration report with warnings/errors.

---

## UX Modernization Checklist
- [ ] Clear onboarding and first-run empty states
- [ ] Keyboard-first operation for frequent logging
- [ ] Undo/redo and non-destructive editing
- [ ] Contextual help and tooltips replacing long-form instructions
- [ ] Consistent design system (spacing, typography, color tokens)
- [ ] Accessibility support (contrast, focus order, screen reader labels)
- [ ] Offline-first reliability with transparent save/recovery behavior

---

## Validation Strategy

### Parity validation
- Compare desktop outputs to Perl baseline for:
  - trend values
  - variance calculations
  - chart data points
  - import/export round-trip consistency

### Reliability validation
- Stress-test large multi-year logs.
- Simulate abrupt shutdowns during writes.
- Verify recovery and no data loss.

### UX validation
- Conduct task-based usability tests:
  1) create profile
  2) enter month data
  3) inspect trend
  4) export data
- Track completion rate, time-on-task, and error frequency.

---

## Suggested Milestones and Timeline (example)
1. **M1 (2–3 weeks)**: Discovery complete, architecture frozen, project scaffolded.
2. **M2 (3–4 weeks)**: Core calculations ported with parity tests.
3. **M3 (3–4 weeks)**: Storage layer + migration pipeline operational.
4. **M4 (4–6 weeks)**: UI feature-complete for core workflows.
5. **M5 (2–3 weeks)**: Interop hardening, QA, and release prep.

_Total: ~14–20 weeks for a production-ready first desktop release with a small team._

---

## Immediate Next Actions (starting now)
- [ ] Confirm C++/Qt6 as target stack (or choose alternative and update this plan).
- [ ] Create `docs/migration/feature-parity.md` by auditing `src/HDiet/*.pm` and `src/hdiet.js`.
- [ ] Extract 20–30 representative calculation fixtures from current Perl implementation.
- [ ] Draft SQLite schema v1 and migration wizard UX wireframes.
- [ ] Stand up initial CMake skeleton and CI matrix.
