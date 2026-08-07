# Changelog

All notable changes to the Release Ledger are documented here. Dates are UTC.

## 2026-08-07 — Public beta backfill

### Added
- **Public beta tracking.** A new `PUBLIC_BETA` data table plus lookup/rendering logic (`publicBetaFor()`, updates to `pillOf()` and the detail card) surfaces, for any beta build that has one, which public beta round it corresponds to and when it went out to public testers.
- **128 verified public beta entries**, backfilled newest-first from iOS/iPadOS 27.0 down through iOS 9.0 (Apple's first public beta cycle) — covering most `.0` releases and many point releases (17.4, 18.4, 26.1–26.5, etc.), not just major versions.
- Every entry is cross-checked against a real build in AppleDB's live iOS calendar feed (2,021 events); no date was added without a matching build.

### Fixed
- **iOS 27.0 / iPadOS 27.0 public beta 1 divergence.** The two OSes did *not* share a build for this round: iOS re-seeded developer beta 3 (`24A5380h`, 2026-07-06) unchanged, while iPadOS shipped its own distinct build (`24A5380l`, 2026-07-13). The data model now supports OS-specific overrides so this renders correctly for both platforms.

### Discovered
- **iOS 16.0 / iPadOS 16.1 public beta 5 divergence (2022-08-23).** iOS's fifth public beta stayed labeled "16.0," while iPadOS's fifth public beta was seeded under the "16.1 beta 1" label — a genuine version-track split, not just a build split. Both sides are now recorded correctly.

### Changed
- Footer credits expanded to include the AppleDB/emiyl MIT attribution, a MacRumors sourcing credit, and an Apple non-affiliation notice, alongside the existing Lee Pears medallion credit.

### Known limitations
- **iOS/iPadOS 26.6 public beta 5** is intentionally left unresolved. MacRumors' 2026-07-13 article title carries an "[Update: Public Beta Available]" suffix without stating whether the public seed landed that same day or the next — rather than guess, this entry is omitted. Absence of an entry means "not yet researched," never "no public beta existed."
- A handful of other rounds (documented inline in the `PUBLIC_BETA` comments) were left out for the same reason — the sourcing didn't cleanly resolve to one build and date.
- Sourcing for 2025–2026 entries (26.x point releases, 27.0) is internally consistent with the real AppleDB build calendar but is newer and harder to independently corroborate than the pre-2025 history; worth a spot-check against MacRumors over time.

### Testing
- Rebuilt the jsdom validation harness: full-feed parse coverage, detail-card render for all 2,021 builds (no throws, no `undefined`/`NaN` leakage), gap-audit against real date deltas, all 16 filter-toggle combinations, full pagination, offline/unreachable-source degradation, and targeted checks on both divergence cases plus the deliberately-omitted 26.6 beta 5. 25 assertions, all passing.
