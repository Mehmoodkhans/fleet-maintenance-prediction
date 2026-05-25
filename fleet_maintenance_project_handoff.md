# 🚛 Fleet Maintenance Cost Prediction — Project Handoff Document

> **Purpose:** This document is the complete handoff for continuing the Fleet Maintenance Cost Prediction project in a new chat session. It contains everything needed to pick up exactly where Phase 1 left off.

---

## 📋 Project Identity

| Field | Detail |
|---|---|
| **Project Name** | Fleet Maintenance Cost Prediction |
| **Notebook File** | `02_data_setup.ipynb` |
| **Database** | `logistics.db` (SQLite) |
| **Data Folder** | `LO/` (14 CSV files) |
| **Charts Folder** | `charts/` (14 PNG files) |
| **Current Phase** | Phase 1 Complete — Moving to Phase 2 |
| **ML Goal** | Binary classification: Deteriorating vs Stable/Improving trucks |

---

## 📁 File Inventory

### Notebook
```
02_data_setup.ipynb        — Main project notebook (Phase 1 complete)
```

### Charts Folder (`charts/`)
```
Challenge 1 (4 charts)
├── c1_avg_cost_by_make.png
├── c1_total_spend_pie.png
├── c1_downtime_by_make.png
└── c1_heatmap.png

Challenge 2 (3 charts)
├── c2_repair_frequency_bar.png
├── c2_tier_distribution_pie.png
└── c2_lifetime_cost_by_tier.png

Challenge 3 (2 charts)
├── c3_avg_mpg_by_make.png
└── c3_scatter_mpg_vs_cost.png

Challenge 4 (2 charts)
├── c4_scatter_utilization_vs_cost.png
└── c4_tier_cost_bar.png

Challenge 5 (3 charts)
├── c5_annual_spend_breakdown.png
├── c5_cost_trend_by_type.png
└── c5_fleet_trajectory_pie.png

Total: 14 charts
```

### HTML Report (to be built)
```
phase1_report.html         — Phase 1 documentation (not yet built)
```

---

## 🎨 Design System — Logistics Color Palette

All charts and the HTML report use this palette consistently:

```python
PALETTE = {
    'background':   '#f0f4f8',   # Light blue-grey background
    'card':         '#ffffff',   # White card surface
    'navy':         '#1e3a5f',   # Deep navy — primary
    'blue':         '#2d7dd2',   # Mid blue — secondary
    'teal':         '#06d6a0',   # Teal green — success/stable
    'yellow':       '#ffd166',   # Yellow — warning
    'red':          '#ef476f',   # Pink red — danger/deteriorating
    'text':         '#1e3a5f',   # Navy text
    'subtext':      '#5a7184',   # Blue grey subtext
}

# 6 make colors — one per truck brand (consistent order)
MAKE_COLORS = [
    '#1e3a5f',  # Navy      — Freightliner (sorted alphabetically)
    '#2d7dd2',  # Blue      — International
    '#06d6a0',  # Teal      — Kenworth
    '#ffd166',  # Yellow    — Mack
    '#ef476f',  # Red       — Peterbilt
    '#7b68ee',  # Purple    — Volvo
]
```

### Chart Standards Applied
- Y axis label: horizontal, positioned top-left using `set_label_coords(-0.08, 1.02)`
- X axis label: horizontal below ticks using `labelpad=8`
- Top y tick removed to prevent overlap with value labels
- Bold tick labels using `label.set_fontweight('bold')`
- Insight annotation at bottom using `ax.annotate(xy=(0.5, -0.12))`
- All charts saved at `dpi=150` with `facecolor=PALETTE['background']`

---

## 📊 Phase 1 — Key Findings Summary

### Challenge 1 — Maintenance Cost by Make & Type
| Finding | Value |
|---|---|
| Highest avg cost make | Mack — $2,039 per repair |
| Lowest avg cost make | Volvo — $1,864 per repair |
| Cost spread across makes | Only ~9% ($175) — make is a weak predictor |
| Most expensive repair type | Tire — $2,054–$2,495 across all makes |
| Cheapest repair type | Inspection — $479–$545 (~4x cheaper than others) |
| Highest total fleet spend | Freightliner — $1,115,814 |
| Downtime hours | Nearly identical across all makes (24–25 hrs) |

### Challenge 2 — Repair Frequency
| Finding | Value |
|---|---|
| Most repaired truck | TRK00003 (Peterbilt 2018) — 41 repairs, $90,161 |
| Original thresholds (15+/8-14/<8) | ❌ Placed 98.3% in one tier — unusable |
| Recalibrated High (≥27 repairs) | 38 trucks (31.7%) — avg $60,213 lifetime |
| Recalibrated Medium (20–26) | 56 trucks (46.7%) — avg $45,582 lifetime |
| Recalibrated Low (<20 repairs) | 26 trucks (21.7%) — avg $34,227 lifetime |
| Cost gap High vs Low | $25,986 — 1.76x more expensive |

### Challenge 3 — Fuel Efficiency
| Finding | Value |
|---|---|
| MPG spread across fleet | Only 0.11 MPG (6.45–6.56) — negligible |
| Highest avg MPG | Mack — 6.51 |
| Lowest avg MPG | Volvo — 6.49 |
| MPG vs maintenance cost | No meaningful relationship — scatter confirms |
| ML verdict | `average_mpg` — weak feature, likely to be dropped |

### Challenge 4 — Utilization Rate
| Finding | Value |
|---|---|
| Fleet utilization range | 0.7578–0.8907 (13.3% spread) |
| Tier distribution (recalibrated) | 30 High / 31 Medium / 31 Low (≈ equal thirds) |
| High tier avg monthly cost | $1,351 |
| Medium tier avg monthly cost | $1,222 — cheapest (U-shaped pattern) |
| Low tier avg monthly cost | $1,349 |
| Key seasonal finding | October consistently most expensive month |
| February | Consistently cheapest month across all 3 years |
| ML verdict | `utilization_rate` — moderate feature, keep but low importance |

### Challenge 5 — Cost Over Time
| Finding | Value |
|---|---|
| Peak spend year | 2023 — $1,974,809 |
| Parts vs labor ratio | ~78% parts / ~22% labor consistently |
| Rising cost types | Engine and Transmission — upward trend 2022→2024 |
| Most deteriorated truck | TRK00040 — +$2,217 increase |
| Most improved truck | TRK00007 — -$1,358 decrease |
| Fleet trajectory split | **Exactly 50/50** — 60 Deteriorating / 60 Stable |
| Data quality issue | TRK00011 — NaN for 2024, no maintenance records |

---

## 🔑 ML Features Identified — Phase 2 Input Table

| Feature | Type | Source Table | Expected Importance | Notes |
|---|---|---|---|---|
| `cost_trajectory` | **Binary Target** | maintenance_records | — Target | Deteriorating=1, Stable=0 |
| `maintenance_type` | Categorical | maintenance_records | 🔴 High | One-hot encode |
| `cost_change_2022_to_2024` | Numeric | maintenance_records | 🔴 High | Engineered feature |
| `total_repairs` | Numeric | maintenance_records | 🔴 High | Raw count per truck |
| `repair_tier` | Categorical | maintenance_records | 🟠 Medium | Recalibrated: ≥27/20-26/<20 |
| `truck_make` | Categorical | trucks | 🟠 Medium | One-hot encode |
| `truck_age` | Numeric | trucks | 🟠 Medium | 2024 minus model_year |
| `utilization_rate` | Numeric | truck_utilization_metrics | 🟠 Medium | Avg per truck |
| `month` / `season` | Categorical | truck_utilization_metrics | 🟠 Medium | October spike pattern |
| `avg_downtime_hrs` | Numeric | maintenance_records | 🟡 Low | Test then likely drop |
| `average_mpg` | Numeric | trips | 🟡 Low | Test then likely drop |

---

## ⚙️ Phase 2 Roadmap — What Needs to Be Built Next

### Step 1 — Build Master Feature Table
Join all relevant tables into one flat dataframe — one row per truck:

```python
# Tables to join:
# maintenance_records (aggregated per truck)
# trucks (make, model_year, status)
# truck_utilization_metrics (avg utilization per truck)
# trips (avg MPG per truck)

# Key aggregations needed:
# - avg_cost per truck per year (2022, 2023, 2024)
# - cost_change_2022_to_2024 (engineered)
# - cost_trajectory (binary target: Deteriorating=1 / Stable=0)
# - total_repairs per truck
# - repair_tier per truck (recalibrated: ≥27 / 20-26 / <20)
# - avg utilization_rate per truck
# - truck_age (2024 - model_year)
```

### Step 2 — Data Cleaning
- Handle TRK00011 — missing 2024 records (NaN in cost_trajectory)
- Check nulls across all feature columns
- Handle outliers in total_cost
- Verify all 120 trucks are present (92 have utilization records, 120 have maintenance)

### Step 3 — Feature Engineering
```python
# Features to create:
# truck_age = 2024 - model_year
# cost_change_2022_to_2024 = cost_2024 - cost_2022
# cost_trajectory = 1 if cost_2024 > cost_2022 else 0
# repair_tier = High/Medium/Low based on recalibrated thresholds
# season = Spring/Summer/Autumn/Winter from month
# is_active = 1 if status == 'Active' else 0

# Encoding:
# maintenance_type → one-hot (7 categories)
# truck_make → one-hot (6 categories)
# repair_tier → ordinal (Low=0, Medium=1, High=2)
# season → one-hot (4 categories)
```

### Step 4 — Train/Test Split
```python
# Recommended split: 80/20
# Stratify on cost_trajectory to maintain 50/50 balance
# Random state: 42 (for reproducibility)
```

### Step 5 — Baseline Model
```python
# Start with Logistic Regression as baseline
# Then Random Forest
# Then XGBoost
# Compare using: Accuracy, Precision, Recall, F1, ROC-AUC
```

### Step 6 — Feature Importance
```python
# Use Random Forest feature_importances_
# Plot top 10 features
# Confirm or challenge our Phase 1 hypotheses:
# - Does maintenance_type come out on top?
# - Does average_mpg rank low as predicted?
# - Does cost_change rank high as predicted?
```

### Step 7 — Results & Business Interpretation
- Which trucks should be prioritised for preventive maintenance?
- What is the estimated cost saving if deteriorating trucks are caught early?
- Plain English summary for non-technical audience

---

## 🚨 Known Issues & Decisions Made

| Issue | Decision |
|---|---|
| TRK00011 missing 2024 data | Drop from ML dataset or impute with 2023 value |
| JOIN between trips and maintenance creates Cartesian product | Always use `AVG(DISTINCT m.total_cost)` not `AVG(m.total_cost)` |
| Original repair frequency tiers too low | Recalibrated to ≥27/20-26/<20 based on actual distribution |
| Original utilization tiers too low | Recalibrated to ≥0.845/0.820-0.844/<0.820 |
| average_mpg has only 0.11 spread | Expected to be dropped after correlation testing |
| downtime_hours nearly identical across fleet | Expected to be dropped after correlation testing |
| 28 trucks missing from utilization table (120 vs 92) | Investigate before building master feature table |

---

## 💬 How to Start the New Chat

Paste this exact message at the start of the new chat session:

---

*"I am continuing a Data Science project on Fleet Maintenance Cost Prediction. Phase 1 (SQL EDA — 5 challenges) is complete. I need help with Phase 2: Data Preparation and Feature Engineering leading into ML modelling.*

*Key facts:*
- *Database: logistics.db (SQLite), 14 tables, 120 trucks*
- *Target variable: cost_trajectory (binary — Deteriorating vs Stable/Improving, 50/50 split)*
- *Notebook: 02_data_setup.ipynb*
- *Phase 1 complete — all EDA done, features identified*

*Please read the attached handoff document before we begin: fleet_maintenance_project_handoff.md*

*Start with Step 1 — building the master feature table."*

---

## 📎 Attachments to Include in New Chat

When starting the new chat, attach:
1. `02_data_setup.ipynb` — so the new session can see all Phase 1 work
2. `fleet_maintenance_project_handoff.md` — this document
3. Optionally: any of the 14 charts from `charts/` folder if visual context is needed

---

## ✅ Phase 1 Completion Checklist

- [x] Project introduction and objective written
- [x] Dataset overview documented
- [x] Challenge 1 — Cost by Make & Type (queries + observations + 4 charts)
- [x] Challenge 2 — Repair Frequency (queries + recalibration + 3 charts)
- [x] Challenge 3 — Fuel Efficiency (queries + observations + 2 charts)
- [x] Challenge 4 — Utilization Rate (queries + observations + 2 charts)
- [x] Challenge 5 — Cost Over Time (queries + observations + 3 charts)
- [x] All observations filled with real data
- [x] All 14 charts in logistics theme saved to charts/ folder
- [x] Phase 1 summary table written
- [x] ML features table written
- [x] Notebook cleaned and audited
- [ ] HTML documentation report (in progress — current chat)
- [ ] Phase 2 — Data Preparation (next chat)
- [ ] Phase 3 — ML Modelling (next chat)
- [ ] Phase 4 — Results & Interpretation (next chat)

---

*Document created at end of Phase 1 — Fleet Maintenance Cost Prediction Project*
*Notebook: 02_data_setup.ipynb | Database: logistics.db | Charts: 14 files in charts/*
