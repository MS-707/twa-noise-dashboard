# TWA Noise Exposure Assessment Dashboard

## Project Overview
Single-file HTML dashboard (`index.html`, ~2600 lines) for presenting occupational noise exposure data to an ELT (executive leadership team) audience. Deployed on Vercel via GitHub auto-deploy from `MS-707/twa-noise-dashboard` (main branch).

- **Vercel Project ID**: `prj_iB2zxTGVxwU3JTz5ZHAvfc0Fxty9`
- **Team ID**: `team_ey0RPugNvHwxXgc7X92ifzKa`
- **Live URL**: https://twa-noise-dashboard.vercel.app
- **Rollback tag**: `v1.0-pre-viz-overhaul`

## Architecture
- **Single HTML file** with inline CSS + JS (no build step, no framework)
- **Chart.js 4.5.1** for line, bar, stacked bar, and area charts
- **D3.js 7.9.0** for heatmaps and sparklines
- **CSS custom properties** for light/dark theming (`--bg`, `--card`, `--purple`, etc.)
- **`getC()` function** reads CSS vars at runtime so charts are theme-reactive
- **`rebuildAssessmentCharts()`** destroys and rebuilds all Chart.js + D3 charts on theme toggle
- All data is embedded inline (`DATA.summary`, `HEATMAP_DATA`, `DATE_LABELS`)

## File Structure
Everything is in `index.html`:
1. **Lines 1-18**: Head, meta tags, CDN scripts (Chart.js, D3, date-fns adapter with SRI hashes)
2. **Lines 19-1150**: `<style>` block (CSS variables, components, responsive breakpoints, print styles)
3. **Lines 1151-1850**: HTML structure (header, hero, 3 tab panels, footer, tooltip div)
4. **Lines 1851-1870**: `getC()` color function and embedded data objects
5. **Lines 1870-2600**: JavaScript (chart builders, toggles, theme system, animations, init)

## Tab Structure & Section Order

### Assessment Tab (`#tab-assessment`)
1. **Key Findings** (`#key-findings`) - Auto-generated compliance summary
2. **Location Cards** (`.loc-grid`) - KPI cards for Bullpen + Build Area
3. **Daily TWA Comparison** (`#chart-twa-bars`) - Bar chart, the headline number
4. **Hourly Noise Levels** (`#chart-overlay`) - Line chart with location/date toggles
5. **Noise Exposure Duration** (`#time-in-zone-chart`) - Stacked bars showing time-in-zone
6. **Distribution + Dose** (`.chart-row`) - Side-by-side histogram and dose accumulation
7. **Sparklines** (`#sparkline-grid`) - Mini dose profiles per location/date
8. **Results Table** (`#summary-tbody`) - Full metrics table
9. **Heatmaps** (`#heatmap-bullpen`, `#heatmap-build`) - Minute-level D3 heatmaps

### Methodology Tab (`#tab-methodology`)
- Instrumentation, mathematical framework, cross-validation checks, FAQ

### Guide Tab (`#tab-guide`)
- Plain-language "What This Means for You" for non-technical readers

## Theme System
- Light: white backgrounds, slate text, purple accent
- Dark: true black (`#000000` bg, `#111111` cards), neutral grays, no blue tint
- Toggle persists in localStorage, auto-detects `prefers-color-scheme`
- `applyTheme()` updates `data-theme` attribute, rebuilds all charts via `rebuildAssessmentCharts()`

## Key CSS Patterns
- `.page { max-width: 1328px; padding: 24px }` - Content area = 1280px, aligned with hero-inner
- `.hero { padding: 40px 24px 0 }` + `.hero-inner { max-width: 1280px }` - Header content
- `.chart-section` - Card container with 24px padding, 12px border-radius, purple left-accent on title
- `.date-btn` / `.date-btn.active` - Toggle buttons with purple active state
- `.ts-badge` - Verified/Approximate timestamp badges with hover tooltips (disabled on touch via `@media (hover: none)`)
- Breakpoints: 1024px (tablet), 768px (2-col to 1-col), 640px (mobile), 480px (small phone)

---

## Design Decisions: What Worked

### Audience-first content ordering
Reordering sections so the executive takeaway (Key Findings, TWA numbers) comes first, with supporting detail (hourly patterns, heatmaps) progressively deeper. ELT readers get the answer in 5 seconds.

### True black/white dark mode
Blue-slate dark mode (`#0F172A`) felt corporate-generic. Switching to `#000000` with neutral grays created a cleaner, more modern aesthetic. User strongly preferred this.

### Consolidated single Assessment tab
Originally had 4 tabs (Assessment, Methodology, Advanced Visualizations, Guide). Removing the Advanced Viz tab and folding Time-in-Zone + Heatmaps into Assessment reduced clicks and kept all data in one scrollable view.

### Key Findings card
Auto-generated from data — zero exceedances, margin below action level, per-location averages, peak context. Green accent. Gives the compliance answer immediately without reading any chart.

### Softened ">90 dBA" language
Changed to "90+ dBA" with amber color (#B45309) instead of red. The data shows zero time in this zone — alarming language for a non-event undermines credibility with ELT audience.

### CSS custom properties for everything
Every color referenced by JS charts comes through `getC()` → CSS variables. Theme toggle rebuilds charts instantly. No hardcoded colors in JS except the noise zone palette (which is semantic, not theme-dependent).

### Time-in-Zone stacked bars
Intuitive at a glance — wide green bands = safe. Computed from minute-level HEATMAP_DATA. Each bar totals 100% of the shift.

### Minute-level heatmaps
The most granular view — every minute of every day, color-coded by dBA. Good for identifying specific noisy periods. D3 rendering with interactive tooltips.

### Location toggle on overlay chart
Instead of two separate charts, one chart with "Both / Bullpen Only / Build Area Only" buttons. Reduces visual clutter, enables direct comparison.

### Hover tooltips on timestamp badges
"Verified" and "Approximate" badges explain data provenance inline. Non-intrusive but available on demand.

## Design Decisions: What Didn't Work

### Ridgeline chart
Too tall, lines overlapped the x-axis labels, and the density visualization wasn't intuitive for non-technical readers. Removed entirely.

### Dumbbell comparison chart (L10/L50/L90)
Comparing statistical percentiles across locations is meaningful to acousticians but not ELT. The metric names alone require explanation. Removed.

### Blue-slate dark mode
First dark mode attempt used Tailwind-style slate blues (`#0F172A`, `#1E293B`). User found it "not my favorite" — felt like a generic dark theme rather than a deliberate design choice.

### Separate Advanced Visualizations tab
Created friction — users had to discover and click a 4th tab to see Time-in-Zone and Heatmaps. Better to have one long scrollable Assessment with progressive detail.

### Lazy-init charts on tab switch
Originally heatmaps only rendered when the Advanced Viz tab was first opened. This created a visible loading delay. Building everything on page load (with the single-file architecture) is fast enough and eliminates the jank.

### Red danger color for >90 dBA zone
Using `#DC2626` (red) for the >90 dBA zone in the Time-in-Zone chart was alarming when all measurements were well below that threshold. Amber (#B45309) communicates "this is the highest zone" without implying danger.

### 9px table headers on mobile
Below readable minimum. Bumped to 11px. Mobile tables should never go below 10px for any text.

### `--text-muted` for chart descriptions
`#94A3B8` (light) / `#666666` (dark) had marginal contrast. Switched to `--text-secondary` for chart descriptions — they're content, not decoration.

## Principles Learned

1. **ELT reads top-down, stops early.** Put the compliance answer first. Supporting data can scroll below.
2. **Statistical charts need translation.** L10/L50/L90, ridgelines, and percentile comparisons are expert tools. For executives, use plain concepts: "time spent in each zone", "daily average vs. limit".
3. **Dark mode should be intentional.** Don't just invert a blue theme. True black with neutral grays reads as a deliberate design choice.
4. **Softened language preserves credibility.** If data shows compliance, alarming labels ("dangerous", red colors) for zones with zero exposure undermines the report's tone.
5. **One scrollable view > multiple tabs.** Tabs hide content. For a report that tells a story, keep the narrative in one flow with progressive detail.
6. **CSS variables + runtime reads = free theme reactivity.** Investing in `getC()` early meant dark mode was a CSS-only change — no chart code modifications.
7. **Alignment math matters on wide viewports.** If header and content use different max-width/padding patterns, they'll diverge at >1280px. Match the pattern: content max-width should account for padding (1280 + 2*24 = 1328px).
8. **Touch devices don't hover.** Any `:hover` tooltip needs `@media (hover: none)` to prevent persistent popups on mobile tap.
9. **Mobile tab bars need scroll cues.** `overflow-x: auto` alone doesn't tell users more tabs exist. Add a fade gradient and lock to horizontal with `overflow-y: hidden` + `overscroll-behavior-x: contain`.
10. **Chart.js + D3 coexist well.** Use Chart.js for standard chart types (line, bar, stacked bar) and D3 for custom visualizations (heatmaps, sparklines). Don't fight either library's strengths.
