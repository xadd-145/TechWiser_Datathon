# Synchrony Contact Center Forecasting Challenge

### UIUC Statistics Datathon 2026

Built a forecasting pipeline to predict **30-minute interval contact center demand** across **4 portfolios** for **August 2025**, using historical **daily** and **intraday interval** data.

This project was developed during the **UIUC Statistics Datathon 2026** in collaboration with **Synchrony**, with the objective of forecasting:

* **Call Volume (CV)**
* **Customer Care Time (CCT)**
* **Abandon Rate (ABD)**

The competition framing went beyond pure forecasting accuracy. Since forecast errors directly affect **staffing, service levels, and operational cost**, the project was approached as a **business-aware workforce planning problem**, not just a modeling exercise.

---

## Team

* **Aditi Patil**
* **Viral Sheth**
* **Dhyey Kasundra**

We had previously collaborated as consultants at **BIG (Business Intelligence Group)**, which helped us move quickly through data debugging, modeling iteration, and decision-making under time pressure.

---

## Problem Statement

Forecast intraday contact center demand at **30-minute granularity** across **4 portfolios** for all of **August 2025**.

### Required outputs

For each portfolio and interval, generate:

* Calls Offered / Call Volume
* Abandoned Calls
* Abandon Rate
* Customer Care Time

### Why this problem was interesting

This was not just a time-series task. It was a forecasting problem under **real operational constraints**:

* Under-forecasting increases understaffing risk
* Over-forecasting increases cost inefficiency
* Workload depends jointly on **Volume × CCT**
* The leaderboard scoring was **asymmetric**, so business-aware calibration mattered

---

## Repository Structure

```text
TechWisers_Datathon/
│
├── Aditi/
│   ├── Wrangling.ipynb
│   ├── Interval_Repair.ipynb
│   ├── Modeling1.ipynb
│   ├── Method2.ipynb
│   ├── Method3_NewStrategies.ipynb
│   └── Method4_ReverseEngineered.ipynb
│
├── Viral/
│   └── Viral.ipynb
│
├── dhyey_data_analysis.ipynb
│
├── Data/
│   ├── Raw/
│   └── Cleaned/
│
└── Leadership_experimental/
    ├── daily_patterns.png
    ├── forecast_abd_plus_003_day.csv
    ├── forecast_billing_cycle_v1.csv
    ├── forecast_cct_minus_05.csv
    ├── forecast_cct_minus_10.csv
    ├── forecast_cct_minus_20.csv
    ├── forecast_abd_minus_001.csv
    ├── forecast_abd_minus_002.csv
    ├── forecast_abd_minus_003.csv
    ├── forecast_abd_plus_002_day.csv
    └── forecast_abd_plus_003_peak.csv
```

---

## Project Workflow

The work evolved in stages, from raw data inspection to submission engineering and leaderboard-driven experimentation.

### 1. Initial data understanding

We began by auditing both the **daily** and **interval-level** datasets across portfolios A–D.

This phase focused on:

* schema inspection
* null analysis
* date parsing
* column standardization
* identifying inconsistencies across files

---

### 2. Interval reconstruction and repair

The interval data was not immediately usable. Several issues had to be addressed before modeling:

* missing interval rows
* duplicate date-slot combinations
* incomplete daily 48-slot grids
* structured null runs
* unstable overnight values
* inconsistent relationships across CV, abandoned calls, ABD, and CCT

We rebuilt complete **48-slot daily grids** for April–June and applied safer, time-aware imputation rather than naive forward fill.

---

### 3. Daily and intraday forecasting baseline

We then built a forecasting pipeline that separated the problem into two parts:

#### Daily anchors

Forecast daily values such as:

* call volume
* CCT
* abandon rate

#### Intraday distribution

Learn how each day’s total demand distributes across 48 time slots using:

* day-of-week patterns
* slot-wise intraday profiles
* portfolio-specific shapes

This decomposition-based design was more stable than directly predicting every interval row independently.


---

### 4. Submission generation and validation

Once interval forecasts were generated, outputs were converted into the required submission format and validated for:

* no negative values
* correct schema
* portfolio-level completeness
* metric consistency
  (`Abandoned Calls = Abandon Rate × Calls Offered`)

This phase also included rounding and CSV export logic.


---

### 5. Leaderboard-driven experimentation

After baseline submissions, we iterated using multiple controlled strategy notebooks to test targeted changes such as:

* time-of-day boosts
* workload-aware adjustments
* abandoned rate nudges
* CCT reduction strategies
* reverse engineering of stronger submissions
* business-hour and billing-cycle corrections

These iterations were driven by both:

* observed leaderboard behavior
* the competition’s asymmetric penalty structure


---

## Modeling Approach

Our final approach was not a single model, but a **forecasting system**.

### Core ideas

* Use **daily forecasts** as anchor estimates
* Learn **intraday slot-share profiles** from cleaned historical interval data
* Forecast interval metrics by distributing daily totals using learned shapes
* Apply **business-aware calibration** instead of only optimizing raw statistical fit

### Important design choices

* Reconstructed missing interval grids before modeling
* Used **portfolio-specific** behavior rather than a global shared pattern
* Treated overnight low-volume windows carefully to avoid noise amplification
* Enforced internal consistency between volume, abandon rate, and abandoned calls
* Incorporated asymmetric risk into forecast adjustments

---

## What We Observed

Some of the most important findings from the project were not about model complexity, but about system behavior.

### Data observations

* Missingness was often **structured**, not random
* Several days did not contain all **48 intervals**
* Overnight metrics could be highly unstable due to very low call counts
* Portfolio behaviors differed materially in both shape and scale

### Modeling observations

* A decomposition-based approach outperformed naive direct interval prediction
* Intraday demand profiles carried strong signal
* Forecasting decisions improved once we aligned them to the **cost of error**, not just average fit
* The business meaning of the objective function mattered as much as the model

---

## Key Files

### Aditi

* `Wrangling.ipynb`
  Raw data loading, schema cleanup, profiling, diagnostics

* `Interval_Repair.ipynb`
  Interval auditing, deduplication, full-grid reconstruction, safe imputation

* `Modeling1.ipynb`
  Daily feature engineering, baseline forecasting, intraday profile generation, submission assembly

* `Method2.ipynb`
  Submission comparison, validation, rounding, experimentation helpers

* `Method3_NewStrategies.ipynb`
  Alternative strategy testing including time-of-day and workload-aware boosts

* `Method4_ReverseEngineered.ipynb`
  Reverse-engineering stronger submission patterns and targeted tactical adjustments

### Viral

* `Viral.ipynb`
  Baseline daily and interval profile forecasting workflow, early submission generation

### Dhyey

* `dhyey_data_analysis.ipynb`
  Exploratory data analysis on daily data, missingness inspection, cleaning, and initial diagnostics

---

## Data

This repository is organized around two main data layers:

### `Data/Raw/`

Contains the original competition files:

* portfolio daily data
* portfolio interval data
* staffing data

### `Data/Cleaned/`

Contains cleaned and reconstructed datasets used in downstream modeling.

> Note: Competition data may be subject to usage restrictions depending on Datathon guidelines. If sharing publicly, verify whether raw files should remain private and add them to `.gitignore` if needed.

---

## Experimental Outputs

The `Leadership_experimental/` folder contains targeted CSV variants and exploratory outputs used during submission iteration, such as:

* CCT decrease experiments
* abandoned rate upward/downward nudges
* billing-cycle corrected submissions
* daily pattern visualizations

These files capture the trial-and-evaluate loop used during leaderboard optimization.

---

## Results

Our team stayed in the **Top 10 on the live leaderboard through most of the competition**, and the project became a strong example of how **data engineering, forecasting, and business-aware calibration** interact in real decision systems.

More importantly, this project reinforced a principle we care deeply about:

> Good models predict.
> Great models understand systems.

---

## Lessons Learned

* Preprocessing is not a pre-step. It is part of the modeling strategy.
* Forecast quality depends on understanding the operational system, not just choosing algorithms.
* Competition objectives can encode business logic that should shape model design.
* The fastest path to improvement is often better framing, not more complexity.

---

## Future Improvements

If extended beyond the Datathon setting, the next steps would include:

* better probabilistic forecasting for uncertainty-aware staffing
* explicit hierarchical modeling across portfolio and interval levels
* automated experiment tracking
* backtesting by rolling forecast windows
* packaging the notebook workflow into reusable Python modules

---

## Acknowledgments

Thanks to:

* **UIUC Statistics Department**
* **Synchrony**
* **AWS**
* **Databricks**

for organizing a high-quality applied forecasting challenge.

---
