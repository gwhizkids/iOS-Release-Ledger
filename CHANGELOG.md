# Changelog

All notable changes to the Release Ledger are documented here. Dates are UTC.

## 2026-08-07 — macOS support, OS toggle, contrast fix

### Added
- **macOS build data.** A second AppleDB feed (`ios/macOS/main.ics`, with the same mirror fallback chain as the iOS feed) is now fetched in parallel alongside the existing iOS/iPadOS feed. The two feeds fail independently — if one is unreachable, the other still loads and the status line says which source is missing rather than showing nothing.
- **OS toggle.** A new segmented row (iOS / iPadOS / macOS) sits above the existing Public/Beta/RC/SDK row, each pill tinted with its own accent color. Toggling an OS off filters it out of the results list and the version rail's counts, mirroring how the release-type toggles already worked.
- 2,696 macOS builds spanning Mac OS X 10.0 (2000) through the current macOS release, parsed by the existing ICS parser with three additional recognized OS-name prefixes (`Mac OS X`, `OS X`, `macOS`).

### Fixed
- **Grey text contrast.** `--label-2` and `--label-3` (the site's secondary/tertiary text colors) failed WCAG AA contrast against the page's background gradient — `--label-3` measured as low as 2.76:1 in spots. Both are now ≥4.5:1 against all three gradient stops while keeping the existing visual hierarchy between them.

### Changed
- **Gap/cadence tracking now buckets by product, not by era-specific branding.** macOS's three historical names (`Mac OS X`, `OS X`, `macOS`) are treated as one continuous line for "days since the last build" math, so a macOS 10.x build doesn't get treated as a "first build" just because it fell on the other side of a rebrand from the previous one.
- Subtitle updated to mention macOS and the earlier 2000 start date.

### Known limitations
- **The medallion's rings are version-number-agnostic across OSes** (a pre-existing property of the visualization, now inherited by macOS): macOS's major "10" spans 2001–2019, so it shares a ring with iOS's much shorter-lived major "10" (2016–2017). Toggling an OS off does not currently thin out the medallion itself, only the rail/list below it — same behavior the release-type toggles have always had.

### Testing
- Extended the jsdom harness with 35 assertions covering: mixed-feed parsing (iOS/iPadOS/macOS all present), correct OS bucketing for all three macOS-era name prefixes, unified (non-fragmented) cadence tracks across macOS rebrands, OS-toggle click wiring and its interaction with the existing type-toggle wiring, combined-filter correctness, rail-count filtering, medal render stability, and graceful degradation for both a single-feed outage and a total outage. All 35 passing.

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
