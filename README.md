# TWA Noise Exposure Assessment Dashboard

A single-page interactive dashboard for occupational noise exposure analysis built with Chart.js and D3.js. Presents OSHA TWA, NIOSH TWA, noise dose, and statistical noise metrics from field measurements — with independently verified results and full methodological transparency.

**Live:** Deployed on Vercel via git push.

---

## What This Project Demonstrates

This dashboard was built entirely through a collaboration between a EHS professional (Mark Starr, CSP) and **Claude Opus 4.6** (Anthropic's CLI agent). It represents a shift in what's possible for in-house environmental health and safety work — not as a replacement for third-party assessment, but as a demonstration of how AI-assisted tooling can raise the floor of what a single practitioner can accomplish.

### The Traditional EHS/IH Workflow

Occupational noise assessment has historically required:

- **Specialized software licenses** — proprietary noise analysis platforms (Quest NoisePro, Larson Davis, etc.) that cost thousands annually
- **Consultant engagements** — CIH-credentialed industrial hygienists billing $150–300/hr for measurement, analysis, and reporting
- **Black-box calculations** — results delivered in PDF reports where the underlying math is opaque and non-reproducible
- **Weeks of turnaround** — from field measurement to final deliverable
- **Static deliverables** — flat reports that can't be interrogated or re-visualized

None of this is wrong — third-party assessment by qualified professionals remains the standard for regulatory compliance, litigation support, and complex exposure scenarios. **This project doesn't replace that.**

### What Claude Opus 4.6 Changes

What it *does* change is the **accessibility of the intermediate work** — the data processing, statistical analysis, visualization, and documentation that sits between raw field data and actionable insight.

With Claude as a working partner, a single CSP was able to:

| Capability | Traditional Approach | With Claude Opus 4.6 |
|---|---|---|
| **Raw data processing** | Proprietary software or manual Excel | Purpose-built Python script, stdlib-only, fully auditable |
| **TWA/dose calculation** | Calculator or vendor software | Explicit implementation of 29 CFR 1910.95 formulas with 7 validation checks |
| **Independent verification** | Send to second consultant | AI-assisted verification script that recomputes every metric from scratch |
| **Interactive visualization** | Static charts in Word/PDF | Responsive web dashboard with Chart.js + D3 (heatmaps, radial plots, ridgelines) |
| **Methodology documentation** | Written by hand, often minimal | Peer-review-ready methodology section with full mathematical framework |
| **Limitations disclosure** | Varies by practitioner | Structured academic-style limitations section, 7 categories |
| **Accessibility/ARIA compliance** | Rarely considered | Full ARIA tab pattern, keyboard navigation, focus states, reduced-motion support |
| **Deployment** | Email PDF attachment | Vercel deploy, shareable URL, version-controlled |

### What's Actually Democratized

1. **Mathematical rigor without a math background.** The dashboard includes complete derivations of Leq, OSHA dose, NIOSH dose, TWA conversions, and statistical percentiles. Claude didn't just compute these — it explained the exchange rate differences between OSHA (5 dB) and NIOSH (3 dB), why Jensen's inequality validates Leq calculations, and why TWA < Leq when a threshold is applied. An EHS generalist can now engage with the acoustics math at a level that previously required specialized training.

2. **Verification as a first-class concern.** The Python verification script (`noise_analysis.py`) runs 7 independent cross-checks per data file — Jensen's inequality, Leq recomputation, dose recomputation, TWA back-calculation, sample count verification, percentile ordering, and NIOSH >= OSHA consistency. 21 checks across 910,393 data points, all passing. This level of self-validation is unusual even in consultant deliverables.

3. **Transparent limitations.** The dashboard explicitly discloses 7 categories of methodological limitations — from the Build Area timestamp assumption to the 0.1-second MAX reduction bias to the distinction between area monitoring and personal dosimetry. This kind of candor is standard in academic publishing but rare in operational EHS reports.

4. **Data presentation that respects the data.** When the verification process revealed that one microphone lacked embedded timestamps, the dashboard doesn't hide this — it marks that location's time-axis data as "Approximate" with amber badges and italic disclaimers throughout. Honesty in data presentation over aesthetics.

### What This Does NOT Replace

To be explicit:

- **This is not a substitute for personal dosimetry** per 29 CFR 1910.95(d)(1). These are area measurements from fixed microphones, not personal exposure assessments.
- **This is not a formal compliance determination.** A qualified industrial hygienist (CIH) or Certified Safety Professional (CSP) should interpret results in context.
- **The 3-day measurement window may not be representative** of long-term exposure patterns across different project phases, seasons, or crew configurations.
- **Microphone calibration and DAQ accuracy are assumed** per manufacturer specifications — no independent calibration verification was performed.
- **This tool assists EHS practitioners — it does not replace professional judgment.** The AI helps with computation, visualization, and documentation; the human provides context, field knowledge, and regulatory interpretation.

---

## Technical Details

### Stack
- **Frontend:** Vanilla HTML/CSS/JS (single file, no build step)
- **Charts:** Chart.js 4.5.1 + D3.js 7.9.0
- **Verification:** Python 3.x, standard library only (`math`, `sys`, `os`, `datetime`)
- **Deployment:** Vercel (auto-deploy from `main`)
- **Data:** PCB 130F21 microphones → Dewesoft IOLite 8xACC DAQ → DewesoftX reduced-data export

### Dashboard Tabs
1. **Assessment** — KPI cards, hourly overlay charts, distribution histograms, cumulative dose curves, daily TWA comparison, detailed results table
2. **Advanced Visualizations** — D3 ridgeline density plots, polar/radial hourly maps, minute-resolution heatmaps
3. **Methodology & Verification** — Instrumentation specs, mathematical framework (Leq, OSHA/NIOSH dose/TWA, percentiles), 6-step data pipeline, 21-check verification matrix, 7 limitations, 8-question FAQ

### Accessibility
- ARIA tab pattern with `role="tablist/tab/tabpanel"`, `aria-selected`, `aria-controls`
- Arrow-key keyboard navigation between tabs
- FAQ accordion with `role="button"`, `aria-expanded`, keyboard Enter/Space support
- `:focus-visible` outlines on all interactive elements
- `prefers-reduced-motion` media query

---

## The Bigger Picture

The EHS profession has a tooling gap. Industrial hygiene calculations are well-defined (the math hasn't changed since the 1970s) but the tools to perform, verify, and present that math remain locked behind expensive licenses or consultant relationships. Most in-house EHS teams operate with Excel spreadsheets and institutional knowledge.

AI agents like Claude Opus 4.6 don't replace the expertise — they remove the *implementation barrier*. An EHS professional who understands regulatory requirements, what a TWA means and why it matters can now produce a verified, transparent, interactive analysis from raw field data in an afternoon instead of waiting weeks for a consultant report.

The goal isn't to cut corners. It's to raise the standard of what "good enough for internal use" looks like — from a static spreadsheet with no verification to a peer-reviewable, version-controlled, publicly deployable analysis with full methodological documentation.

That's the democratization. Not cheaper. Better.

---

## License

Internal use. Assessment data is from an actual workplace noise study (February 2026). Equipment specifications and regulatory thresholds are public domain (OSHA, NIOSH).

---

*Built with [Claude Code](https://claude.ai/claude-code) (Claude Opus 4.6) as an EHS/AI collaboration experiment.*

