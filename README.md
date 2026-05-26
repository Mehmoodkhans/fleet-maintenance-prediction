# 🚛 Fleet Maintenance Cost Prediction

> A complete end-to-end Data Science project analysing a logistics fleet database to predict which trucks are likely to incur high maintenance costs — from SQL exploration through feature engineering to machine learning.

[![Phase](https://img.shields.io/badge/Phase%201-Complete-06d6a0?style=flat-square)](https://github.com/Mehmoodkhans/fleet-maintenance-prediction)
[![Phase](https://img.shields.io/badge/Phase%202-In%20Progress-ffd166?style=flat-square)](https://github.com/Mehmoodkhans/fleet-maintenance-prediction)
[![Language](https://img.shields.io/badge/Language-Python%20%7C%20SQL-2d7dd2?style=flat-square)](https://github.com/Mehmoodkhans/fleet-maintenance-prediction)
[![Database](https://img.shields.io/badge/Database-SQLite-1e3a5f?style=flat-square)](https://github.com/Mehmoodkhans/fleet-maintenance-prediction)

---

## 📋 Project Overview

This project analyses a logistics fleet operations database covering **120 trucks**, **14 interconnected tables** and **3 years of operations data (2022–2024)** to answer one core business question:

> *"Can we predict which trucks will have escalating maintenance costs — before those costs become a major financial burden?"*

The ML goal is a **binary classification model** that classifies each truck as:
- 🔴 **Deteriorating** — cost trajectory increasing year over year
- 🟢 **Stable/Improving** — cost trajectory flat or declining

A key finding from Phase 1 is that the fleet splits **exactly 50/50** between these two classes — a perfectly balanced classification target requiring no resampling techniques.

---

## 🗺️ Project Phases

| Phase | Description | Status |
|---|---|---|
| **Phase 1** | SQL Exploration & EDA — 5 Focused Challenges | ✅ Complete |
| **Phase 2** | Data Preparation & Feature Engineering | 🔄 In Progress |
| **Phase 3** | ML Modelling — Classification & Regression | ⏳ Pending |
| **Phase 4** | Results, Interpretation & Business Insights | ⏳ Pending |

---

## 📊 Phase 1 — EDA Report

A full interactive HTML report covering all 5 challenges is available here:

**👉 [View Phase 1 Report](https://mehmoodkhans.github.io/fleet-maintenance-prediction/fleet_maintenance_phase1_report.html)**

The report includes:
- 15 charts in a consistent logistics colour theme
- Key findings and ML implications per challenge
- Feature importance predictions for Phase 2
- Phase 2 roadmap

---

## 🗄️ Dataset

| Field | Detail |
|---|---|
| **Source** | [Logistics Operations Database — Kaggle](https://www.kaggle.com/datasets/yogape/logistics-operations-database) |
| **Tables** | 14 interconnected CSV files |
| **Fleet Size** | 120 trucks across 6 makes |
| **Data Period** | January 2022 — December 2024 |
| **Total Records** | 2,920 maintenance events |
| **Total Spend** | $5.73M across all repair types |

**Key tables used in this project:**

| Table | Description |
|---|---|
| `maintenance_records` | All repair events — cost, type, downtime |
| `trucks` | Fleet inventory — make, model year, status |
| `truck_utilization_metrics` | Monthly performance — utilization rate, revenue |
| `trips` | Individual trip logs — distance, MPG, fuel |
| `drivers` | Driver roster — experience, employment status |

> **Note:** The raw dataset is not included in this repository. Download it directly from [Kaggle](https://www.kaggle.com/datasets/yogape/logistics-operations-database) and place the CSV files in a `LO/` folder in the project root.

---

## 🔍 Phase 1 — Key Findings

### Challenge 1 — Maintenance Cost by Make & Type
- **Mack** trucks are the most expensive to maintain at **$2,039/repair** on average
- Cost spread across all makes is only **~9% ($175)** — truck make is a weak predictor
- **Inspection** repairs cost **~4x less** than all other types ($479–$545 vs $2,000–$2,400)
- `maintenance_type` emerged as the **strongest categorical signal** in Phase 1

### Challenge 2 — Repair Frequency
- **TRK00003** (Peterbilt 2018) leads with **41 repairs** and **$90,161 lifetime cost**
- Original tier thresholds were recalibrated from (15+/8-14/<8) to **(≥27/20-26/<20)** — original thresholds placed 98.3% of trucks in a single tier
- Recalibrated tiers show a clean **$25,986 cost gap** between High and Low frequency trucks
- **Kenworth** is the only make absent from the Top 20 most-repaired trucks

### Challenge 3 — Fuel Efficiency
- Fleet MPG spread is only **0.11 MPG** (6.45–6.56) across the entire fleet
- **No meaningful relationship** between MPG and maintenance cost confirmed by scatter plot
- `average_mpg` will be **dropped** as an ML feature after correlation testing

### Challenge 4 — Utilization Rate
- **U-shaped cost pattern** discovered — medium utilization trucks cost least to maintain
- **October** is consistently the most expensive month across all 3 years
- **February** is consistently the cheapest month
- `season` / `month` will be engineered as a feature in Phase 2

### Challenge 5 — Cost Over Time
- Fleet splits **exactly 50/50** — 60 Deteriorating vs 60 Stable trucks
- **Engine and Transmission** costs show consistent upward trend 2022→2024
- Most deteriorated truck: **TRK00040** — cost increased **+$2,217** from 2022 to 2024
- Parts cost consistently **~78%** of total spend vs **~22%** labor

---

## 🔑 ML Features Identified

| Feature | Type | Expected Importance |
|---|---|---|
| `cost_trajectory` | **Binary Target** | 🎯 Target Variable |
| `maintenance_type` | Categorical | 🔴 High |
| `cost_change_2022_to_2024` | Numeric (engineered) | 🔴 High |
| `total_repairs` | Numeric | 🔴 High |
| `repair_tier` | Categorical (ordinal) | 🟠 Medium |
| `truck_make` | Categorical | 🟠 Medium |
| `truck_age` | Numeric (engineered) | 🟠 Medium |
| `utilization_rate` | Numeric | 🟠 Medium |
| `month` / `season` | Categorical (engineered) | 🟠 Medium |
| `avg_downtime_hrs` | Numeric | 🟡 Low — test then drop |
| `average_mpg` | Numeric | ❌ Drop — confirmed weak |

---

## 📁 Repository Structure

```
fleet-maintenance-prediction/
│
├── 📓 02_data_setup.ipynb              # Main project notebook — Phase 1
├── 📄 fleet_maintenance_phase1_report.html  # Interactive HTML report
├── 📄 fleet_maintenance_project_handoff.md  # Phase 2 handoff document
├── 📄 README.md                        # This file
│
└── 📁 charts/                          # All 15 visualisations
    ├── c1_avg_cost_by_make.png
    ├── c1_total_spend_pie.png
    ├── c1_downtime_by_make.png
    ├── c1_heatmap.png
    ├── c2_repair_frequency_bar.png
    ├── c2_tier_distribution_pie.png
    ├── c2_lifetime_cost_by_tier.png
    ├── c3_avg_mpg_by_make.png
    ├── c3_scatter_mpg_vs_cost.png
    ├── c4_scatter_utilization_vs_cost.png
    ├── c4_tier_cost_bar.png
    ├── c5_annual_spend_breakdown.png
    ├── c5_cost_trend_by_type.png
    ├── c5_inspection_cost_trend.png
    └── c5_fleet_trajectory_pie.png
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Python 3** | Core language |
| **SQLite** | Database engine |
| **Pandas** | Data manipulation |
| **Matplotlib / Seaborn** | Visualisation |
| **Scikit-learn** | ML modelling (Phase 3) |
| **XGBoost** | Gradient boosting (Phase 3) |
| **Jupyter Notebook** | Development environment |

---

## ⚙️ How to Run

```bash
# 1. Clone the repository
git clone https://github.com/Mehmoodkhans/fleet-maintenance-prediction.git
cd fleet-maintenance-prediction

# 2. Install dependencies
pip install pandas matplotlib seaborn scikit-learn xgboost jupyter

# 3. Download dataset from Kaggle and place CSV files in LO/ folder

# 4. Launch the notebook
jupyter notebook 02_data_setup.ipynb
```

---

## 📈 What's Next — Phase 2

- Build master feature table joining all relevant tables
- Handle TRK00011 missing 2024 records
- Engineer new features: `truck_age`, `cost_change_2022_to_2024`, `season`
- One-hot encode categorical variables
- Train/test split (80/20 stratified)
- Baseline model → Random Forest → XGBoost
- Feature importance analysis to validate Phase 1 hypotheses

---

## 👤 Author

**Mehmood Ahmed Khan**
- GitHub: [@Mehmoodkhans](https://github.com/Mehmoodkhans)
- LinkedIn: [Mehmood Ahmed Khan](https://www.linkedin.com/in/mehmoood)

---

*This project is actively being developed. Phase 2 coming soon.*
