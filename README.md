# Global Renewable Energy & India Power Generation Reliability
An end-to-end data analytics project combining a global view of the renewable energy transition with a deep-dive into India's power generation reliability, using unit-level government data and a tested hypothesis about why India's generation gap has grown.

📊 [View the interactive Tableau dashboard](https://public.tableau.com/app/profile/neeredha.p.arun/viz/IndiaPowerGenerationGlobalRenewableEnergyAnalysis/Dashboard1)

## Problem Statement
How does the world's shift toward renewable electricity compare across major economies, and — zooming into India specifically — is the country's power generation actually delivering what it plans to, or is the gap between planned and actual output growing, and if so, why?

## Data Sources

**India Power Generation — Ministry of Power / National Power Portal**
- Source: Ministry of Power, Government of India, published via the National Power Portal
- Accessed via: India Data Portal (a mirror of the official `npp.gov.in/dgrReports` extraction)
- Raw size: ~2.58 million rows, unit-level daily generation records, 2017–2025
- License/rights: Published as open government data

**Global Electricity Mix — Our World in Data**
- Source: Our World in Data Energy dataset, compiled from Energy Institute's Statistical Review, Ember, EIA, and Shift Dataportal
- Raw size: 23,377 rows (220 countries + regional/income aggregates), 1900–2025
- License/rights: Open data, freely licensed

**Recent Global Generation — CarbonMonitor-Power**
- Source: CarbonMonitor-Power, a peer-reviewed dataset (Scientific Data)
- Raw size: 128,117 rows, 17 countries, 2024–2026
- License/rights: Published under a fair-use open data policy

## Data Cleaning & Methodology
All cleaning and exploratory analysis was done in Python (pandas) in Google Colab; the cleaned datasets were then connected in Tableau Public for the interactive dashboards.

**Scope narrowing:**
- Separated ~12,645 rows tagged `region == 'Bhutan Imp.'` — these are hydropower *imported* from Bhutan, not domestic Indian generation — into their own file rather than mixing them into the reliability analysis
- Removed ~94 regional/income-group aggregates from the OWID dataset (e.g. "World", "Asia", "Africa (EI)" vs. "Africa (EIA)" vs. "Africa (Ember)") using the dataset's `iso_code` field, which is populated only for real countries — left in, these would have double-counted against actual countries in any ranking or comparison
- Restricted cross-country rankings to 2023, the most recent year with complete 220-country coverage in the OWID data (2024 has 199/220 countries reporting, 2025 only 90/220 — using either as a "current" snapshot would bias the ranking toward whichever countries report fastest)

**Data quality issues found and resolved:**
- Dropped 42 exact duplicate rows (same date, station, and unit) and 3 rows missing generation values from the India dataset — both negligible (<0.002% of rows)
- Found ~161,000 rows with `monitored_capacity` of 0 or missing; investigated rather than dropped, and confirmed the *same* power stations report correct capacity on most days and 0/missing on others (e.g. a real 1,000MW hydro plant) — this is a data-entry gap for that specific day, not a genuine zero-capacity plant. Flagged these rows rather than deleting them, so generation totals stay intact while capacity-dependent ratios exclude them cleanly
- Removed 5 junk rows from the CarbonMonitor export (3 blank rows, plus the site's own URL and a date stamp that had been pulled into the data)

**Core metric:**
- Computed `shortfall = planned generation − actual generation` as a signed value (positive = under-delivery, negative = over-delivery), aggregated to state-month and source-type-month level for the dashboards

**Reliability thresholds:**
- The "least/most reliable states" ranking excludes the smallest 25% of states by total planned generation, so a tiny grid's noisy percentage doesn't distort the comparison
- The "fastest renewable transitions" ranking only includes the 75 countries with data for both 1985 and 2023, and flags that very small grids (e.g. Luxembourg, Lithuania) can show large percentage swings from small absolute changes

## Key Findings

**India's renewable share of electricity actually fell** from 27.8% in 1985 to 19.3% in 2023 — not because renewable generation shrank, but because coal-based generation grew even faster during India's industrialization, diluting renewables' share of a much larger total.

**India's Thermal power plants show a real, growing gap between planned and actual generation** — national shortfall rose from roughly 1% in 2017 to over 5% by 2025, with month-to-month volatility roughly doubling over the same period. Breaking this down by source type showed the growth is driven by Thermal specifically (which carries ~83% of India's generation), not by the smaller, more volatile Gas Turbine category that looked like the obvious culprit at first glance.

**I tested a specific hypothesis for why Thermal's shortfall is growing** — that grid operators are increasingly backing down thermal plants to accommodate growing renewable supply. Comparing India's renewable share against Thermal's shortfall year by year, the correlation is effectively zero (r ≈ -0.01, p = 0.98). The theory doesn't hold up, and the cause remains open — a tested and ruled-out explanation rather than an assumed one.

**State-level rankings need a caveat**: Delhi shows the highest shortfall of any major state, but its entire local generation fleet is three small gas plants — most of Delhi's actual electricity supply is imported from other states, so this reflects the volatility of a small, gas-dependent fleet rather than the reliability Delhi residents actually experience.

**Germany stands out globally**, gaining 51 percentage points of renewable electricity share since 1985 — far ahead of China (+8) and the US (+10.5) — while small, historically hydro-dependent countries (Bhutan, Nepal, Brazil) top the raw "most renewable" list without having undergone an active transition at all.

## Tools Used
- Python (pandas) — data cleaning, aggregation, and the correlation test
- Google Colab — notebook environment
- Tableau Public — interactive dashboard (chosen for this project to build hands-on experience with a second BI tool)

## Repository Structure
```
├── README.md
├── scripts/
│   ├── EDA_of_Global_Energy.ipynb
│   ├── EDA_of_India_Power.ipynb
└── data_samples/
    └── carbonmonitor_sample.csv
    └── daily-power-generation_sample.csv
    └── owid-energy-data_sample.csv
```

## Dashboard Preview
The interactive Tableau dashboard includes three pages:
- **Global Snapshot (2024–2026)**: renewable share ranking, generation mix by country, and renewable-rank shifts over time
- **Long-Term Transition (1985–2025)**: India vs. peer countries' renewable trajectory, and the fastest-transitioning countries worldwide
- **India Deep-Dive**: national planned-vs-actual generation trend, state-level reliability ranking, shortfall by generation source, and the renewable-vs-shortfall correlation test

[View the live dashboard on Tableau Public](https://public.tableau.com/app/profile/neeredha.p.arun/viz/IndiaPowerGenerationGlobalRenewableEnergyAnalysis/Dashboard1)
