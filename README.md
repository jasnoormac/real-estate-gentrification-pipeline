# Atlanta Gentrification Tracker
### Property Appreciation Analysis via Automated Public Records Scraping — Summerhill, Atlanta, GA

---

## Overview

This project builds an end-to-end data pipeline to quantify gentrification pressure at the parcel level in Atlanta's **Summerhill neighbourhood** — a historically Black community experiencing rapid redevelopment since the construction of Georgia State's stadium and BeltLine-adjacent investment.

Using an async Playwright scraper, **400 randomly sampled parcels** are pulled from the Fulton County qPublic portal, extracting sale history, multi-year valuations, and physical property attributes. The core metric — **Adjusted Sale %** — measures percentage price appreciation between two sale events per parcel, normalised to the property's effective age.

The analysis explores how renovation recency, building size, lot area, and bedroom count relate to price appreciation, producing interactive visualisations and regression models to surface patterns in displacement risk.

> Developed as part of graduate research at **Georgia State University**, advised by Dr. Chetan Tiwari.

---

## Key Results

- Computed **Adjusted Sale %** across 400 parcels to measure parcel-level appreciation
- Properties with lower Delta values (recently renovated relative to age) showed higher appreciation
- Smaller square footage ranges (1,000–2,000 sqft) concentrated the highest avg appreciation
- Multiple linear regression (Delta, Bedrooms, Sq Ft, Acres) yielded a baseline predictive model
- Interactive dashboard enables real-time filtering for exploratory analysis

---

## Repository Structure

```
atlanta-gentrification-tracker/
│
├── qpublic_local.py                      # Async Playwright scraper — run locally
├── qpublic_scraper_analysis.ipynb        # Full analysis notebook (Google Colab)
├── qpublic_400_parcels_v2_wrangled.xlsx  # Wrangled input data (upload to Drive)
├── .gitignore
└── README.md
```

---

## Pipeline Architecture

```
qPublic Portal (Fulton County)
        │
        ▼
┌─────────────────────┐
│  Playwright Scraper  │  ← async, 2 workers, retry logic, checkpoint saves
│  qpublic_local.py   │
└────────┬────────────┘
         │  qpublic_400_parcels_v2.xlsx
         ▼
┌─────────────────────┐
│   Manual Wrangling   │  ← sale pairing, column standardisation
└────────┬────────────┘
         │  qpublic_400_parcels_v2_wrangled.xlsx
         ▼
┌──────────────────────────┐
│  Colab Analysis Notebook  │  ← clean → feature engineer → visualise → model
└──────────────────────────┘
```

---

## Feature Engineering

| Feature | Description |
|---|---|
| `Sale Pre Clean` | Numeric sale price before the study period |
| `Sale Post Clean` | Numeric sale price after the study period |
| `Adjusted Sale %` | `((Post − Pre) / Pre) × 100` — core appreciation metric |
| `Delta` | `Effective Age − Year Built` — renovation recency proxy |

**Data quality filters applied:**
- Rows with negative Delta dropped (effective age cannot precede build year)
- Rows missing either sale price dropped (required for all analysis)
- `$0` and blank sale prices nullified before cleaning

---

## Visualisations

| Chart | Library | Purpose |
|---|---|---|
| Adjusted Sale % Distribution | Matplotlib | Overall appreciation spread across parcels |
| Delta vs Adjusted Sale % | Matplotlib | Renovation age vs price appreciation |
| Avg by Year Built Decade | Matplotlib | Era-based appreciation patterns |
| Sale Pre/Post vs Delta | Plotly | Per-parcel connected price movement |
| Avg by Bedrooms | Plotly | Bedroom count vs appreciation |
| Avg by Square Footage Range | Plotly | Size bucket vs appreciation |
| Avg by Acres Range | Plotly | Lot size vs appreciation |
| Parallel Coordinates | Plotly | Multi-feature interactive exploration |
| Simple Linear Regression (×4) | Plotly | OLS baseline per individual feature |
| Multiple Linear Regression | Plotly | Combined model — coefficients + actual vs predicted |
| Interactive Filter Dashboard | ipywidgets + Matplotlib | Real-time parcel filtering across all 4 panels |

---

## Tech Stack

| Category | Tools |
|---|---|
| Scraping | Python, Playwright (async), Chromium |
| Data Wrangling | Pandas, NumPy, OpenPyXL |
| Visualisation | Matplotlib, Plotly, Seaborn |
| Modelling | Scikit-learn (LinearRegression, R²) |
| Interactivity | ipywidgets |
| Environment | Google Colab, Google Drive |

---

## Getting Started

### 1. Run the Scraper (local)
```bash
pip3 install playwright pandas openpyxl
playwright install chromium
python3 qpublic_local.py
```
The scraper saves progress every 25 parcels to `scrape_progress.json` — safe to interrupt and resume.

### 2. Run the Analysis (Google Colab)
1. Upload `qpublic_400_parcels_v2_wrangled.xlsx` to `My Drive/qpublic_scraper/`
2. Open `qpublic_scraper_analysis.ipynb` in Google Colab
3. Run all cells top to bottom

---

## .gitignore

```
*.xlsx
*.csv
scrape_progress.json
selected_parcels.json
failed_parcels.csv
__pycache__/
.env
```

---

## Research Context

Summerhill is one of Atlanta's oldest neighbourhoods and has faced sustained gentrification pressure following Georgia State University's stadium development and proximity to the BeltLine. This project operationalises displacement risk at the parcel level using publicly available property records, contributing a reproducible data pipeline for neighbourhood-scale gentrification analysis.

---

## Author

**Jasnoor Singh Mac **


[LinkedIn](https://linkedin.com/in/yourprofile) · [GitHub](https://github.com/yourusername)
