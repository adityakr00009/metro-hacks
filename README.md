# TransitPulse 🚇

> **Gujarat Metro Hacks 2024 · Problem Statement PS-13: "The Two-Minute Morning Brief"**

A unified operations intelligence dashboard that automatically detects ridership anomalies, cross-references them with the operational incident log, and generates a structured morning brief — across Metro, BRTS, and AMTS — in seconds, not hours.

---

## The Problem

Every morning, operations managers at Metro, BRTS, and AMTS manually pull reports from three separate systems, paste data into spreadsheets, and look for patterns. This process takes hours. By the time the brief is ready, the morning peak has already passed and critical exceptions have gone unnoticed.

**PS-13 asks:** Can we generate a concise, evidence-backed operations brief automatically?

---

## Solution

**TransitPulse** is a browser-based operations dashboard that:

1. **Ingests data** from Metro, BRTS, and AMTS (CSV import or built-in synthetic data)
2. **Detects anomalies** using a 7-day rolling average baseline with a ±20% threshold
3. **Matches evidence** — each anomaly is automatically cross-referenced with the operational incident log
4. **Generates the brief** — a structured, downloadable plain-text report in one click

Everything runs **offline in the browser**. No Python, no Node.js, no database, no cloud, no installation.

---

## How to Run

1. Open the project folder in VS Code
2. Install the **Live Server** extension (one-time)
3. Right-click `index.html` → **"Open with Live Server"**

The dashboard opens at `http://127.0.0.1:5500` and works fully offline from that point.

---

## Demo Walkthrough

| # | Action | What It Shows |
|---|--------|---------------|
| 1 | Dashboard loads | KPI cards, trend chart, operator donut — populated from synthetic data |
| 2 | Select the anomaly date (Day 10) | Exceptions table: **Vastral Gam −48% HIGH** |
| 3 | Point to "Linked Incident" column | Gate failure shown as *possible* explanation — not a proven cause |
| 4 | Click **"Generate Morning Brief"** | 6-section report: summary, anomalies, incidents, evidence, actions |
| 5 | Click **"↓ Download as .txt"** | Report saved instantly — ready to forward |
| 6 | Import `sample-data/ridership-sample-import.csv` | Live CSV import — dashboard re-analyses in real time |

---

## Project Structure

```
metro-hacks/
├── index.html                          # Dashboard shell
├── src/
│   ├── css/style.css                   # Design system (CSS variables, dark theme)
│   └── js/
│       ├── app.js                      # Entry point — state, events, render loop
│       ├── data.js                     # Data generation, date utilities, CSV parser
│       ├── anomaly.js                  # Anomaly detection + evidence matching
│       ├── charts.js                   # SVG chart renderers (trend, donut, bar)
│       ├── brief.js                    # Morning Brief generator + file download
│       └── ui.js                       # DOM renderers (KPI cards, tables, incidents)
├── sample-data/
│   └── ridership-sample-import.csv     # Demo import file
└── README.md
```

---

## How the Analysis Works

### Anomaly Detection

```
For each station/route on date D:
  baseline = mean(ridership[D-7 to D-1])    ← 7-day rolling average

  deviation = (ridership[D] - baseline) / baseline × 100

  if fewer than 3 prior data points  → skip (insufficient history)
  if baseline == 0                   → skip (division-by-zero guard)
  if |deviation| ≥ 35%               → HIGH severity
  if |deviation| ≥ 20%               → MEDIUM severity
```

### Evidence Matching

```
For each flagged exception:
  candidates = incidents where:
    date     == exception.date
    operator == exception.operator
    location has keyword overlap with station name

  if candidates found → "possible explanation" (correlation noted, causation unconfirmed)
  else               → "no supporting incident — further investigation required"
```

The system never claims an incident *caused* a ridership change. It presents evidence for a human decision-maker to evaluate. This distinction is explicit in every generated report.

### Planted Demo Scenarios

| Day | Operator | Location | Effect | Supporting Incident |
|-----|----------|----------|--------|---------------------|
| Day 10 | Metro | Vastral Gam | −48% ridership | All 6 ticket gates failed 06:45–15:20 |
| Day 12 | BRTS | R-03 Chandkheda–Paldi | +62% ridership | Science City Regional Expo |

---

## Features

| Feature | Status |
|---------|--------|
| 7 KPI cards (total, Δ%, per-operator, exceptions, incidents) | ✅ |
| 14-day SVG trend chart, operator donut, bar charts | ✅ |
| Anomaly detection — 7-day rolling baseline, HIGH/MEDIUM severity | ✅ |
| Evidence matching — incident ↔ anomaly cross-reference | ✅ |
| Morning Brief generator — 6-section plain-text report | ✅ |
| Download brief as `.txt` | ✅ |
| Export full dataset as CSV | ✅ |
| Import CSV (toolbar + drag-and-drop) | ✅ |
| Date selector + operator filter + reset | ✅ |
| Fully offline — zero external dependencies | ✅ |

---

## Technical Constraints Met

| Requirement | Implementation |
|-------------|----------------|
| No Python / Node.js | Pure browser HTML + CSS + JavaScript |
| No database server | Data in JS arrays in memory |
| No cloud API or AI model | Deterministic rule-based algorithms |
| No external CDN, fonts, or images | System font stack; SVG drawn inline |
| Works fully offline | Zero network requests |
| No bundler or build step | Native ES Modules (`type="module"`) |

## Tech Stack

```
HTML5 · CSS3 (Custom Properties, CSS Grid) · Vanilla JavaScript ES2022 · SVG
```

No frameworks. No build tools. No internet required.

---

## Disclaimer

All data is **synthetic and illustrative only**. It does not represent actual statistics or incidents from GMRC, BRTS Ahmedabad, or AMTS. Created for Gujarat Metro Hacks 2024 · PS-13.
