# Noise Exposure Assessment Dashboard

A single-page interactive dashboard for occupational noise exposure analysis, built with Chart.js and D3.js. Presents OSHA TWA, NIOSH TWA, noise dose, and statistical noise metrics from field measurements — with independently verified results and full methodological transparency.

**Live:** [twa-noise-dashboard.vercel.app](https://twa-noise-dashboard.vercel.app)

---

## What This Project Demonstrates

This dashboard was built entirely through a collaboration between a Certified Safety Professional (CSP) and **Claude Code** (Anthropic's agentic CLI tool, powered by Claude Opus 4.6). The CSP is not a software developer. Prior to this project, their coding experience was limited to what's sometimes called "vibe coding" — learning to direct AI tools toward professional goals over the past year.

This project is a case study in what happens when domain expertise meets an AI agent that can execute: a single EHS practitioner, working from raw field data, produced a peer-reviewable, interactive, version-controlled noise exposure analysis — something that traditionally requires specialized software, consultant engagements, or both.

### The Traditional Workflow

Occupational noise assessment has historically required:

- **Specialized software licenses** — proprietary noise analysis platforms (Quest NoisePro, Larson Davis, etc.) costing thousands annually
- **Consultant engagements** — CIH-credentialed industrial hygienists billing $150-300/hr for measurement, analysis, and reporting
- **Black-box calculations** — results delivered in PDF reports where the underlying math is opaque and non-reproducible
- **Weeks of turnaround** — from field measurement to final deliverable
- **Static deliverables** — flat reports that can't be interrogated or re-visualized

None of this is wrong — third-party assessment by qualified professionals remains the standard for regulatory compliance, litigation support, and complex exposure scenarios. **This project doesn't replace that.**

### What Changes With Claude Code

What it *does* change is the **accessibility of the intermediate work** — the data processing, statistical analysis, visualization, and documentation that sits between raw field data and actionable insight.

With Claude Code as a working partner, a single practitioner was able to:

| Capability | Traditional Approach | With Claude Code |
|---|---|---|
| **Raw data processing** | Proprietary software or manual Excel | Purpose-built Python script, stdlib-only, fully auditable |
| **TWA/dose calculation** | Calculator or vendor software | Explicit implementation of 29 CFR 1910.95 formulas with 7 validation checks |
| **Independent verification** | Send to second consultant | Verification script that recomputes every metric from scratch |
| **Interactive visualization** | Static charts in Word/PDF | Responsive web dashboard with Chart.js + D3 heatmaps |
| **Methodology documentation** | Written by hand, often minimal | Peer-review-ready methodology section with full mathematical framework |
| **Limitations disclosure** | Varies by practitioner | Structured limitations section with transparent scope statements |
| **Accessibility** | Rarely considered | ARIA tab pattern, keyboard navigation, focus states, screen reader support |
| **Deployment** | Email PDF attachment | Vercel deploy, shareable URL, version-controlled |

### What the Human Brought

This was not a one-directional process. The CSP's domain knowledge shaped every decision:

- **Caught a formula error** — Claude initially applied NIOSH's 3 dB exchange rate where OSHA's 5 dB rate was required. The CSP identified the discrepancy from regulatory knowledge, not from reading the code.
- **Rejected misleading chart types** — Claude proposed ridgeline plots, dumbbell charts (L10/L50/L90 comparisons), and polar/radial charts. The CSP vetoed each one: executives don't read statistical distributions. Time-in-zone stacked bars replaced all of them.
- **Softened alarming language** — The initial dashboard labeled the >90 dBA zone as "Dangerous" in red. The CSP pointed out that zero time was spent in that zone — alarming language for a non-event undermines credibility with executive audiences. Changed to "90+ dBA" in amber.
- **Identified a data artifact** — A 1-minute recording at hour 16 on one date created a misleading visual dip. The CSP recognized this as a partial-hour artifact from field conditions, not a real noise event. The data point was nulled and the hourly chart was ultimately removed as unnecessary for the compliance narrative.
- **Enforced area vs. personal exposure language** — Claude's initial language implied personal exposure determinations. The CSP corrected this to observational language appropriate for area monitoring scope, which has real legal implications.
- **Built custom review skills** — The CSP encoded 24 EHS dashboard review rules as a Claude Code skill file, teaching the AI to evaluate future dashboards against the same executive-readiness criteria discovered during this project.

### What's Actually Democratized

1. **Mathematical rigor without a math background.** The dashboard includes complete derivations of Leq, OSHA dose, NIOSH dose, TWA conversions, and statistical percentiles. Claude explained the exchange rate differences between OSHA (5 dB) and NIOSH (3 dB), why Jensen's inequality validates Leq calculations, and why TWA < Leq when a threshold is applied. An EHS generalist can now engage with acoustics math at a level that previously required specialized training.

2. **Verification as a first-class concern.** The Python verification script runs 7 independent cross-checks per data file — Jensen's inequality, Leq recomputation, dose recomputation, TWA back-calculation, sample count verification, percentile ordering, and NIOSH >= OSHA consistency. 21 checks across 910,393 data points, all passing. This level of self-validation is unusual even in consultant deliverables.

3. **Transparent limitations.** The dashboard explicitly discloses methodological limitations — from timestamp assumptions to the distinction between area monitoring and personal dosimetry. This kind of candor is standard in academic publishing but rare in operational EHS reports.

4. **Presentation that respects the audience.** Every design decision was filtered through a single question: will an executive leadership team member understand this in 5 seconds? Compliance answer first, supporting data below, no statistical charts without translation. The result reads like a report, not a data dump.

### What This Does NOT Replace

- **This is not a substitute for personal dosimetry** per 29 CFR 1910.95(d)(1). These are area measurements from fixed microphones, not personal exposure assessments.
- **This is not a formal compliance determination.** Results should be interpreted in regulatory context by a qualified professional.
- **The 3-day measurement window may not be representative** of long-term exposure patterns across different project phases or crew configurations.
- **This tool assists EHS practitioners — it does not replace professional judgment.** The AI handles computation, visualization, and documentation; the human provides context, field knowledge, and regulatory interpretation.

---

## Technical Details

### Stack
- **Frontend:** Vanilla HTML/CSS/JS (single file, no build step, no framework)
- **Charts:** Chart.js 4.5.1 + D3.js 7.9.0
- **Theming:** CSS custom properties with `getC()` runtime reader for theme-reactive charts
- **Verification:** Python 3.x, standard library only (`math`, `sys`, `os`, `datetime`)
- **Deployment:** Vercel (auto-deploy from `main`)
- **Data:** PCB 130F21 microphones, Dewesoft IOLite 8xACC DAQ, DewesoftX reduced-data export

### Dashboard Tabs
1. **Assessment** — Key Findings compliance summary, location KPI cards with animated metrics, daily TWA comparison, time-in-zone stacked bars, distribution histograms, cumulative dose curves, sparklines, results table, minute-level D3 heatmaps
2. **What This Means for You** — Plain-language guide for non-technical readers: regulatory context, hearing biology, common concerns Q&A
3. **Methodology & Verification** — Instrumentation specs, mathematical framework (Leq, OSHA/NIOSH dose/TWA, percentiles), 6-step data pipeline, 21-check verification matrix, limitations, FAQ
4. **About This Project** — The CSP + AI collaboration story, specific technical examples, what this enables, limitations and transparency

### Design Principles
- **Compliance answer first.** Key Findings card at the top with green status, supporting charts below.
- **Progressive detail.** Most aggregated (daily TWA) to most granular (minute-level heatmaps) in scroll order.
- **No statistical charts without translation.** Ridgeline plots and percentile comparisons were built and rejected. Time-in-zone bars replaced them.
- **Softened language preserves credibility.** No alarming labels for zones with zero exposure.
- **True black dark mode.** `#000000` background with neutral grays — not a blue-tinted inversion.
- **Single scrollable view.** All assessment data in one flow, no hidden tabs for chart content.

### Accessibility
- ARIA tab pattern with `role="tablist/tab/tabpanel"`, `aria-selected`, `aria-controls`
- FAQ accordion with `role="button"`, `aria-expanded`, keyboard Enter/Space support
- `:focus-visible` outlines on all interactive elements
- `prefers-reduced-motion` media query
- Canvas elements with `role="img"` and descriptive `aria-label` attributes

---

## The Bigger Picture

The EHS profession has a tooling gap. Industrial hygiene calculations are well-defined (the math hasn't changed since the 1970s) but the tools to perform, verify, and present that math remain locked behind expensive licenses or consultant relationships. Most in-house EHS teams operate with Excel spreadsheets and institutional knowledge.

AI agents don't replace the expertise — they remove the *implementation barrier*. An EHS professional who understands what a TWA means and why it matters, who knows the difference between area monitoring and personal dosimetry, who can catch a formula using the wrong exchange rate — that person can now produce a verified, transparent, interactive analysis from raw field data. Not in weeks. In an afternoon.

The CSP who built this is not a developer. They learned to direct AI tools toward professional goals. The result isn't a shortcut — it's a higher standard of what one practitioner can produce: peer-reviewable, version-controlled, publicly deployable, with full methodological documentation and honest limitations disclosure.

That's the force multiplication. Not cheaper. Better.

---

## License

Assessment data is from an actual workplace noise study (February 2026). Equipment specifications and regulatory thresholds are public domain (OSHA, NIOSH).

---

*Built with [Claude Code](https://claude.ai/code) (Claude Opus 4.6)*
