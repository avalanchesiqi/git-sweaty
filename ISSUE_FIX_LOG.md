# Issue Fix Log

Use this file to track when specific issues were fixed and what changed.

## 1) Year card stat boxes left-edge alignment mismatch
- Fixed on: 2026-02-08
- Commit: `40b8f16`
- Issue: The yearly card stat box column was slightly offset left compared to the Activity Frequency stat box column.
- Fix details:
  - Removed the previous logic that shifted Activity Frequency stats to match Year stats.
  - Kept width normalization, but changed alignment ownership so Year stat columns align to Activity Frequency.
  - Ran year-column alignment after stacked-mode offsets so final rendered left edges match.
  - Updated layout flow in `site/app.js` by adjusting `alignFrequencyFactsToYearCardEdge()`, `alignYearStatsToFrequencyEdge()`, and `alignStackedStatsToYAxisLabels()`.

## 2) Year card stat boxes overlapping yearly heatmap at certain widths
- Fixed on: 2026-02-08
- Commit: `79d07ce`
- Issue: At some viewport widths, yearly stat boxes overlaid the yearly heatmap instead of moving below it in one horizontal row aligned to the left side.
- Fix details:
  - Added a dynamic yearly stacking mode in `site/app.js` (`syncYearStackingMode`) to switch yearly cards into stacked layout when side-by-side content would overflow.
  - Added matching stacked CSS in `site/index.html` for `.year-card.year-card-stacked` so stat boxes render underneath the heatmap in a single no-wrap horizontal row, left-pinned.
  - Updated year/frequency edge alignment logic so shifts are applied only when both cards are in matching layout modes, preventing overlap-causing cross-mode offsets.
  - Integrated yearly stacking checks into `alignStackedStatsToYAxisLabels()` before and after alignment passes.

## 3) Stacked stat boxes drifting right at narrower widths
- Fixed on: 2026-02-08
- Commit: `10c3c12`
- Issue: After stats moved under the graphs, both Activity Frequency and Year card stat rows could shift right at certain narrow widths instead of staying pinned left.
- Fix details:
  - Added high-specificity narrow-width left-pinning rules in `site/index.html` for `.year-card .card-stats.side-stats-column` and `.more-stats .more-stats-facts.side-stats-column`.
  - Disabled stacked-path transforms in that narrow-width block to prevent rightward drift (`transform: none`).
  - Bumped the frontend script asset version so the browser picks up the updated behavior immediately.

## 4) Stacked stat rows pinned to y-axis label edge (initial)
- Fixed on: 2026-02-08
- Commit: `6735ae4`
- Issue: In stacked mode, stat rows were pinned to a generic left edge instead of the y-axis label edge.
- Fix details:
  - Added stacked-mode pinning helpers in `site/app.js`.
  - Updated `alignStackedStatsToYAxisLabels()` to pin both Year and Activity Frequency stacked stat rows using measured label positions.
- Revert:
  - `git revert --no-edit 6735ae4`

## 5) Corrected text-edge anchoring for stacked rows
- Fixed on: 2026-02-08
- Commit: `10fc3ec`
- Issue: The initial pinning still misaligned edges; Activity Frequency landed too far right while Year cards landed too far left.
- Fix details:
  - Switched stacked alignment calculations to the y-axis text edge (first visible letter), not the full label-box edge.
  - Adjusted offset baseline to each container content box (excluding padding/border).
- Revert:
  - `git revert --no-edit 10fc3ec`

## 6) Mobile follow-up for stacked row pinning stability
- Fixed on: 2026-02-08
- Commit: `cb34914`
- Issue: Alignment behaved correctly on desktop but still drifted on mobile browsers.
- Fix details:
  - Replaced `Range`-based text-edge detection with font-metric measurement using canvas and computed text alignment in `site/app.js`.
  - Kept the same stacked pinning flow, but made y-label left-edge measurement mobile-stable.
- Revert:
  - `git revert --no-edit cb34914`
