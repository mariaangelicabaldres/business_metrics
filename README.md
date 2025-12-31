# Business Metrics & Reporting System

A modular Python/Jupyter-based analytics system for generating automated business review reports. This system transforms raw data from multiple sources into actionable insights through Weekly Business Reviews (WBR), Monthly Business Reviews (MBR), and Quarterly Business Reviews (QBR).

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [The Problem & Solution](#2-the-problem--solution)
3. [Core Philosophy & Design Principles](#3-core-philosophy--design-principles)
4. [Project Structure](#4-project-structure)
5. [Notebook Details](#5-notebook-details)
6. [End-to-End Data Flow](#6-end-to-end-data-flow)
7. [Metrics Logic & Definitions](#7-metrics-logic--definitions)
8. [How to Run the Project](#8-how-to-run-the-project)
9. [Customization Guide](#9-customization-guide)
10. [Known Limitations & Assumptions](#10-known-limitations--assumptions)
11. [Roadmap & Future Improvements](#11-roadmap--future-improvements)
12. [Who This Project Is For](#12-who-this-project-is-for)
13. [Disclaimer](#13-disclaimer)

---

## 1. Project Overview

### What This Project Does

This system consolidates business metrics from multiple data sources and generates professional, decision-ready reports. It addresses the challenge of manually compiling metrics from disparate sources by automating data aggregation, trend analysis, and report generation.

### Reports Generated

| Report Type | Frequency | Primary Focus | Output Format |
|-------------|-----------|---------------|---------------|
| **WBR** | Weekly | Operational performance, week-over-week changes | .docx |
| **MBR** | Monthly | Trend analysis, goal tracking, month-over-month changes | .docx |
| **QBR** | Quarterly | Strategic review, long-term trends, quarter-over-quarter changes | .docx |

### Key Capabilities

- Automated data cleaning and transformation from multiple sources
- Automatic metric computation across all time periods (daily, weekly, monthly, quarterly, annually)
- Automated chart generation with trend lines (linear regression)
- Rolling averages (6-week, 6-month, 6-quarter)
- Goal tracking with progress visualization
- Period-over-period comparison tables (WoW, MoM)
- Programmatic report generation with consistent formatting

---

## 2. The Problem & Solution

### Before: Manual Process (~8 hours/week for WBR alone)

The previous workflow required extensive manual effort for every reporting cycle:

| Step | Manual Task | Time Impact |
|------|-------------|-------------|
| 1 | Pull raw data from Calendly, Typeform, Fathom, Beehiiv, Google Sheets | Logging into multiple platforms |
| 2 | Clean data (remove duplicates, fix formats, handle missing values) | Error-prone, tedious |
| 3 | Sort and categorize data by date, type, series | Repetitive weekly task |
| 4 | Compute metrics (rates, percentages, totals, averages) | Manual calculations in spreadsheets |
| 5 | Create charts for each metric | Copy-paste into presentation tools |
| 6 | Build comparison tables (this week vs last week) | Manual lookups and formatting |
| 7 | Generate final report document | Assembling everything into Word/PDF |

**Total time: ~8 hours per week** just for the Weekly Business Review.

### After: Automated Process (~30 minutes/week)

| Step | What You Do | What the System Does |
|------|-------------|----------------------|
| 1 | Download raw data exports from source platforms | — |
| 2 | Place files in `data/` folder | — |
| 3 | Update `analysis_ref_date.csv` with current dates | — |
| 4 | Run the notebooks (01-13) | **Automatically**: cleans, sorts, categorizes, computes, charts, tables, reports |

**Total time: ~30 minutes** (mostly downloading raw data).

### What Gets Automated

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  MANUAL (Still Required)          │  AUTOMATED (By This System)            │
├───────────────────────────────────┼─────────────────────────────────────────┤
│  • Download raw data exports      │  • Data cleaning & transformation      │
│  • Place files in data/ folder    │  • Sorting & categorization            │
│  • Update date configuration      │  • Metric computation                  │
│                                   │  • Chart generation                    │
│                                   │  • Table creation (WoW, MoM)           │
│                                   │  • Report document assembly            │
└───────────────────────────────────┴─────────────────────────────────────────┘
```

### Time Savings

| Report | Before (Manual) | After (Automated) | Savings |
|--------|-----------------|-------------------|---------|
| WBR | ~8 hours | ~30 min | **94%** |
| MBR | ~10 hours | ~30 min | **95%** |
| QBR | ~12 hours | ~30 min | **96%** |

---

## 3. Core Philosophy & Design Principles

### Modular Architecture

Each notebook handles one responsibility. This separation allows independent testing, easier debugging, and the ability to re-run specific metrics without affecting others.

```
One Notebook = One Data Domain = One Set of Outputs
```

### CSVs as Intermediate Outputs

Rather than passing data between notebooks in memory, each notebook writes cleaned, aggregated CSVs. This design choice provides:

- **Traceability**: Every metric can be traced back to its source CSV
- **Debugging**: Intermediate outputs can be inspected at any stage
- **Flexibility**: Report generators can pull from any combination of outputs
- **Reproducibility**: Re-running a notebook produces identical CSVs given the same inputs

### Programmatic Report Generation

Reports are generated via `python-docx`, not manual document editing. This ensures:

- Consistent formatting across all reports
- Reproducible outputs
- Easy updates when metrics change
- Version-controlled report logic

### Consistent Time Horizons

WBR, MBR, and QBR share the same metric definitions but at different time scales:

| Aspect | WBR | MBR | QBR |
|--------|-----|-----|-----|
| Rolling Average | 6 weeks | 6 months | 6 quarters |
| Comparison | WoW (Week-over-Week) | MoM (Month-over-Month) | QoQ (Quarter-over-Quarter) |
| Trend Periods | 6, 12, 24 weeks | 6, 12, 24 months | 4, 8, 12 quarters |

### Decision-Ready Insights

The system doesn't just produce charts—it generates narrative insights that explain what changed and whether that change is favorable or unfavorable. Every metric includes context for decision-making.

---

## 4. Project Structure

```
project_root/
├── data/                          # Raw input files (CSVs, Excel)
├── notebooks/                     # Jupyter notebooks (01-13)
├── outputs/                       # Processed metric CSVs by domain
├── visualizations/                # Generated PNG charts by time period
├── reports/                       # Final .docx reports
├── analysis_ref_date.csv          # Date configuration file
├── target_reference.csv           # Goal/target definitions
└── README.md
```

### Folder Contents

| Folder | Contents | Generated By |
|--------|----------|--------------|
| `data/` | Raw exports from Beehiiv, Fathom, Calendly, Typeform, Google Sheets | Manual download |
| `outputs/` | Cleaned CSVs (daily, weekly, monthly, quarterly, annual) | Notebooks 01-08 |
| `visualizations/` | PNG charts organized by metric and time period | Notebooks 01-08 |
| `reports/` | Final .docx business review documents | Notebooks 11-13 |

---

## 5. Notebook Details

### Overview: What Each Notebook Automates

| Notebook | Automates | Outputs |
|----------|-----------|---------|
| 01-08 | Data cleaning, categorization, metric computation, chart generation | CSVs + PNGs |
| 09-10 | Summary table creation for reports (WoW, MoM comparisons) | Summary CSVs |
| 11-13 | Full report document assembly | .docx files |

---

### Data Processing Notebooks (01-08)

#### `01_newsletter_series.ipynb` — Email Campaign Performance

**What it automates:**
- Pulls raw email data and categorizes each email by its series type (Deal, Off-Market, Podcast, Case Study)
- Saves each categorized email series to its own CSV file
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes engagement metrics (open rate, CTR, verified CTR, unsubscribe rate)
- Generates trend charts for each metric

**Input:** `beehiiv_posts_by_date.csv`  
**Outputs:** 
- Categorized CSVs per series
- Blended metrics across all series
- Time-aggregated tables (daily → annual)
- Visualization charts

---

#### `02_newsletter.ipynb` — Landing Page Analytics

**What it automates:**
- Processes raw timestamp data (multiple entries per day) into clean daily aggregates
- Combines hourly records into single daily rows
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes page metrics (pageviews, visits, avg duration, bounce rate)
- Generates trend charts for each metric

**Input:** `fathom_newsletter_site.csv`  
**Outputs:**
- Daily aggregated table
- Time-aggregated tables (weekly → annual)
- Visualization charts

---

#### `03_deal_upgrade.ipynb` — Deal Upgrade Page Performance

**What it automates:**
- Processes raw timestamp data into clean daily aggregates
- Combines two data sources (site visits + conversion events) using date as the join key
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes conversion metrics (visits, events, conversion rate)
- Generates trend charts for visits and conversion rate

**Inputs:** `fathom_deal_upgrade_site.csv`, `fathom_deal_upgrade_events.csv`  
**Outputs:**
- Combined daily table with visits + events
- Time-aggregated tables (weekly → annual)
- Visualization charts

---

#### `04_pro_site.ipynb` — Pro Site Page Performance

**What it automates:**
- Processes raw timestamp data into clean daily aggregates
- Combines two data sources (site visits + conversion events) using date as the join key
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes conversion metrics (visits, events, conversion rate)
- Generates trend charts for visits and conversion rate

**Inputs:** `fathom_pro_site_site.csv`, `fathom_pro_site_events.csv`  
**Outputs:**
- Combined daily table with visits + events
- Time-aggregated tables (weekly → annual)
- Visualization charts

---

#### `05_discovery_intro.ipynb` — Call Tracking (Calendly)

**What it automates:**
- Takes one raw file and categorizes data by call type (Discovery vs Intro)
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes call metrics (booked calls, completed calls, avg lead time)
- Generates trend charts for each call type

**Input:** `calendly_event_data_discovery.csv`  
**Outputs:**
- Separate tables for Discovery and Intro calls
- Combined/blended metrics
- Time-aggregated tables (daily → annual)
- Visualization charts

---

#### `06_sales_tracker.ipynb` — Sales Pipeline Metrics

**What it automates:**
- Processes raw sales data from Google Sheets export
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes pipeline metrics (scheduled calls, live calls, show %, offers, offer %, closes, close %)
- Generates trend charts for each pipeline stage

**Input:** `sales_tracker_inputs.csv`  
**Outputs:**
- Time-aggregated tables (daily → annual)
- Visualization charts for all pipeline metrics

---

#### `07_typeform_submissions.ipynb` — Form Submission Data

**What it automates:**
- Cleans messy raw data by removing unnecessary rows and columns
- Generates a clean CSV with only the metrics needed for analysis
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes submission metrics (total submissions, completion rate by response type)
- Generates trend charts

**Inputs:** `typeform_submissions.csv`, `Xtypeform_leads-cohort.csv`  
**Outputs:**
- Cleaned submission data
- Time-aggregated tables (daily → annual)
- Visualization charts

---

#### `08_general_newsletter.ipynb` — Subscriber Growth & Churn

**What it automates:**
- Processes raw subscriber data
- Creates clean aggregated tables: daily, weekly, monthly, quarterly, annually
- Automatically computes growth metrics (subscribed, unsubscribed, net growth, growth rate, leak rate, CVR%)
- Generates trend charts for growth and churn metrics

**Inputs:** `beehiiv_subscribers.xlsx`, `beehiiv_unsubscribes.xlsx`  
**Outputs:**
- Time-aggregated tables (daily → annual)
- Visualization charts

---

### Analysis Notebooks (09-10)

#### `09_sales_metrics_analysis.ipynb` — Sales Summary Tables

**What it automates:**
- Reads processed CSVs from the `outputs/` folder
- Creates WoW (Week-over-Week) comparison tables with core sales metrics
- Creates MoM (Month-over-Month) comparison tables with core sales metrics
- Formats tables for direct insertion into reports

**Inputs:** Processed CSVs from notebooks 03-06  
**Outputs:** Summary tables (WoW, MoM) for sales metrics

---

#### `10_newsletter_analysis.ipynb` — Newsletter Summary Tables

**What it automates:**
- Reads processed CSVs from the `outputs/` folder
- Creates WoW (Week-over-Week) comparison tables with core newsletter metrics
- Creates MoM (Month-over-Month) comparison tables with core newsletter metrics
- Formats tables for direct insertion into reports

**Inputs:** Processed CSVs from notebooks 01, 02, 08  
**Outputs:** Summary tables (WoW, MoM) for newsletter metrics

---

### Report Generation Notebooks (11-13)

#### `11_wbr_generate_report.ipynb` — Weekly Business Review

**What it automates:**
- Reads all processed CSVs and generated charts
- Assembles complete Word document with:
  - Cover page with date range
  - Executive summary table
  - Newsletter section (charts + insights)
  - Sales section (charts + insights)
  - Consistent formatting throughout
- Generates insight text for each metric (current value, change vs prior period, trend)

**Inputs:** CSVs from `outputs/`, charts from `visualizations/`, dates from `analysis_ref_date.csv`  
**Output:** `Weekly_Business_Review_{date_range}.docx`

---

#### `12_mbr_generate_report.ipynb` — Monthly Business Review

**What it automates:**
- Same as WBR but with monthly data and MoM comparisons
- Includes goal tracking from `target_reference.csv`
- Uses 6-month rolling averages instead of 6-week

**Inputs:** CSVs from `outputs/`, charts from `visualizations/`, dates from `analysis_ref_date.csv`, goals from `target_reference.csv`  
**Output:** `Monthly_Business_Review_{date_range}.docx`

---

#### `13_qbr_generate_report.ipynb` — Quarterly Business Review

**What it automates:**
- Same as MBR but with quarterly data and QoQ comparisons
- Includes goal tracking from `target_reference.csv`
- Uses 6-quarter rolling averages
- Longer trend analysis periods (4, 8, 12 quarters)

**Inputs:** CSVs from `outputs/`, charts from `visualizations/`, dates from `analysis_ref_date.csv`, goals from `target_reference.csv`  
**Output:** `Quarterly_Business_Review_{date_range}.docx`

---

## 6. End-to-End Data Flow

### Pipeline Overview

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Raw Data      │ ──► │   Processing    │ ──► │   Analysis      │ ──► │   Reports       │
│   (data/)       │     │   (01-08)       │     │   (09-10)       │     │   (11-13)       │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
       │                       │                       │                       │
       ▼                       ▼                       ▼                       ▼
   Manual               Clean, Sort,              WoW/MoM                 .docx
   Downloads            Categorize,               Summary                 Reports
                        Compute,                  Tables
                        Chart
```

### Detailed Flow

**Stage 1: Raw Data (Manual)**
- Download exports from Beehiiv, Fathom, Calendly, Typeform, Google Sheets
- Place files in `data/` folder

**Stage 2: Processing (Notebooks 01-08) — AUTOMATED**
- Each notebook reads from `data/`
- Cleans data (handles timestamps, duplicates, missing values)
- Sorts and categorizes by date and type
- Computes all metrics automatically
- Generates time-aggregated CSVs (daily, weekly, monthly, quarterly, annual)
- Creates visualization charts (PNG files)
- Outputs to `outputs/{domain}/` and `visualizations/{domain}_{period}/`

**Stage 3: Analysis (Notebooks 09-10) — AUTOMATED**
- Reads from `outputs/` folders
- Builds WoW and MoM comparison tables
- Formats for report insertion
- Outputs summary CSVs

**Stage 4: Report Generation (Notebooks 11-13) — AUTOMATED**
- Reads `analysis_ref_date.csv` for date ranges
- Reads `target_reference.csv` for goals (MBR/QBR)
- Pulls charts from `visualizations/`
- Pulls data from `outputs/`
- Assembles complete Word document
- Outputs `.docx` files to `reports/`

### Notebook Dependencies

```
Independent (run in any order):
  01, 02, 03, 04, 05, 06, 07

Depends on 02:
  08 (uses daily_newsletter.csv)

Depends on 01-08:
  09 (sales metrics from 03, 04, 05, 06)
  10 (newsletter metrics from 01, 02, 08)

Depends on all above:
  11, 12, 13 (report generators)
```

---

## 7. Metrics Logic & Definitions

### Period-over-Period Changes

**Week-over-Week (WoW)**
```
WoW Change = (This Week Value - Previous Week Value) / Previous Week Value × 100
```

**Month-over-Month (MoM)**
```
MoM Change = (This Month Value - Previous Month Value) / Previous Month Value × 100
```

**Quarter-over-Quarter (QoQ)**
```
QoQ Change = (This Quarter Value - Previous Quarter Value) / Previous Quarter Value × 100
```

### Rolling Averages

Rolling averages smooth out noise and reveal underlying trends.

| Report | Rolling Window | Calculation |
|--------|----------------|-------------|
| WBR | 6-week | Average of last 6 weekly values |
| MBR | 6-month | Average of last 6 monthly values |
| QBR | 6-quarter | Average of last 6 quarterly values |

### Goal Tracking (MBR/QBR)

Goals are defined in `target_reference.csv` and used in MBR/QBR reports.

**Progress Calculation:**
```
Progress % = (Current Value / Target Value) × 100
```

### Inverted Metrics

Some metrics are "lower is better" (inverted color logic):

| Metric | Direction |
|--------|-----------|
| Unsubscribes | Lower = Good (Green) |
| Bounce Rate | Lower = Good (Green) |
| Leak Rate | Lower = Good (Green) |
| Unsubscribe Rate | Lower = Good (Green) |

For these metrics, a decrease is shown in green (favorable) and an increase in red (unfavorable).

---

## 8. How to Run the Project

### Environment Requirements

**Python Version:** 3.10+

**Key Libraries:**
```
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
scipy>=1.10.0
python-docx>=0.8.11
openpyxl>=3.1.0
```

### Step 1: Place Raw Data

Download exports from source platforms and place in `data/` folder:
- `beehiiv_posts_by_date.csv`
- `beehiiv_subscribers.xlsx`
- `beehiiv_unsubscribes.xlsx`
- `fathom_newsletter_site.csv`
- `fathom_deal_upgrade_site.csv`
- `fathom_deal_upgrade_events.csv`
- `fathom_pro_site_site.csv`
- `fathom_pro_site_events.csv`
- `calendly_event_data_discovery.csv`
- `sales_tracker_inputs.csv`
- `typeform_submissions.csv`

### Step 2: Configure Dates

Edit `analysis_ref_date.csv`:

```csv
Analysis Start Date,01/01/2025
Analysis End Date,12/06/2025

Quarter Start Date,01/01/2024
Quarter End Date,12/31/2025

Week Start Date,11/30/2025
Week End Date,12/06/2025

Month Start Date,11/01/2025
Month End Date,11/30/2025

Custom Start Date,11/01/2025
Custom End Date,11/30/2025
```

### Step 3: Run Notebooks

**Quick Start (WBR Only):**
```
Run in order: 01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10 → 11
```

**Full Run (All Reports):**
```bash
# From notebooks/ directory:

# Step 1: Process all data sources
jupyter nbconvert --execute 01_newsletter_series.ipynb
jupyter nbconvert --execute 02_newsletter.ipynb
jupyter nbconvert --execute 03_deal_upgrade.ipynb
jupyter nbconvert --execute 04_pro_site.ipynb
jupyter nbconvert --execute 05_discovery_intro.ipynb
jupyter nbconvert --execute 06_sales_tracker.ipynb
jupyter nbconvert --execute 07_typeform_submissions.ipynb
jupyter nbconvert --execute 08_general_newsleter.ipynb

# Step 2: Build analysis tables
jupyter nbconvert --execute 09_sales_metrics_analysis.ipynb
jupyter nbconvert --execute 10_newsletter_analysis.ipynb

# Step 3: Generate reports
jupyter nbconvert --execute 11_wbr_generate_report.ipynb
jupyter nbconvert --execute 12_mbr_generate_report.ipynb
jupyter nbconvert --execute 13_qbr_generate_report.ipynb
```

### Step 4: Find Reports

Reports are saved to `reports/` folder:
- `Weekly_Business_Review_{date_range}.docx`
- `Monthly_Business_Review_{date_range}.docx`
- `Quarterly_Business_Review_{date_range}.docx`

---

## 9. Customization Guide

### Add a New Metric

1. **Identify the source notebook** based on data domain
2. **Add calculation logic** in the appropriate processing cell
3. **Add to aggregation** (daily → weekly → monthly → quarterly)
4. **Update visualization** section to generate chart
5. **Update report generator** (11/12/13) to include new chart

Example: Adding "Email Bounce Rate" to newsletter metrics
```python
# In 01_newsletter_series.ipynb, after loading data:
df['Bounce Rate'] = df['Bounced'] / df['Sent'] * 100

# Add to aggregation:
weekly_agg['Bounce Rate'] = df.groupby('Week')['Bounce Rate'].mean()
```

### Add a New CSV Source

1. Place CSV in `data/` folder
2. Create new notebook or add to existing domain notebook
3. Load and clean data:
   ```python
   df = pd.read_csv('../data/new_source.csv')
   df['Date'] = pd.to_datetime(df['Date'])
   ```
4. Follow existing patterns for aggregation and output

### Extend WBR → MBR → QBR Consistently

When adding a metric to all three reports:

1. Add to appropriate processing notebook
2. Generate weekly, monthly, AND quarterly aggregations
3. Create charts for all three time periods:
   ```
   visualizations/metric_weekly/
   visualizations/metric_monthly/
   visualizations/metric_quarterly/
   ```
4. Add chart mapping to all three report generators (11, 12, 13)

---

## 10. Known Limitations & Assumptions

### Manual Data Ingestion

This system still requires manual data export from source systems:
- Beehiiv exports downloaded manually
- Fathom Analytics exports downloaded manually
- Calendly data exported manually
- Sales tracker maintained in Google Sheets, exported to CSV

**This is the primary opportunity for Version 2 improvement.**

### CSV Structure Assumptions

- All CSVs use comma separators
- Date columns are parseable by pandas
- Numeric columns don't have currency symbols (clean before loading)
- Column names match exactly what notebooks expect

### Scope Limits

- **Not real-time**: Data must be exported and placed in `data/` folder
- **Not a BI tool**: No interactive dashboards or drill-down capability
- **Single business**: Designed for one business entity
- **English only**: Report text and insights in English

### Date Handling

- Assumes consistent date formats across sources
- Week starts on Sunday (configurable in notebooks)
- Fiscal calendar not supported (calendar quarters only)

---

## 11. Roadmap & Future Improvements

### Version 2: Automated Data Ingestion

The biggest improvement opportunity is eliminating manual data downloads:

- [ ] **Beehiiv API integration**: Auto-pull email and subscriber data
- [ ] **Fathom Analytics API**: Auto-pull website metrics
- [ ] **Calendly API**: Auto-pull call booking data
- [ ] **Google Sheets API**: Auto-pull sales tracker data
- [ ] **Typeform API**: Auto-pull form submissions

### Other Planned Improvements

- [ ] **Scheduling**: Automated weekly/monthly runs via cron or Airflow
- [ ] **Config-driven metrics**: Move metric definitions to YAML/JSON files
- [ ] **Data validation layer**: Add schema validation before processing
- [ ] **Error handling**: More graceful failures with meaningful error messages
- [ ] **Dashboard layer**: Streamlit or Dash app for interactive exploration
- [ ] **Email delivery**: Auto-send reports to stakeholders

### Long-Term Vision

- [ ] **Multi-business support**: Parameterized for multiple clients
- [ ] **Forecasting**: Predictive metrics based on historical trends
- [ ] **Natural language insights**: AI-generated narrative summaries

---

## 12. Who This Project Is For

### Ideal Users

- **Data analysts** who need to produce regular business reports
- **Operators/founders** who want consistent, automated metrics tracking
- **Marketing teams** tracking newsletter and funnel performance
- **Sales teams** monitoring pipeline metrics

### Who Should NOT Use This

- Teams needing real-time dashboards (use a BI tool instead)
- Organizations requiring multi-user collaboration on metrics (use Looker, Tableau, etc.)
- Anyone without Python/Jupyter experience (learning curve required)

### When a BI Tool Would Be More Appropriate

Consider Looker, Tableau, Power BI, or Metabase if you need:
- Real-time data refresh
- Self-service exploration
- Multiple users with different access levels
- Interactive drill-down capability
- Embedded analytics

### A Note on Customization

This project was built for a specific business's metrics and reporting needs. The data sources, metric definitions, and report structures reflect that organization's operational requirements. However, the underlying architecture—modular notebooks, CSV intermediates, programmatic report generation—can serve as a foundation for similar analytics systems. If adapting this project, expect to modify data ingestion logic, metric calculations, and report templates to match your specific business context.

---

## 13. Disclaimer

This metrics system is designed for internal decision support. Important considerations:

- **Directional, not precise**: Metrics are calculated programmatically but depend on input data quality. Small discrepancies with source systems are expected.

- **Data quality dependent**: Outputs are only as good as inputs. Verify source data accuracy before making critical business decisions.

- **Not a substitute for judgment**: This system surfaces trends and changes. Interpretation and action require human judgment and business context.

- **No warranty**: This project is provided as-is. The authors are not responsible for decisions made based on its outputs.

---

## 📧 Contact

**Maria Angelica Baldres**  

[LinkedIn Profile](https://www.linkedin.com/in/mariaangelicabaldres/)

---
**Version:** 1.0.0  
**Last Updated:** December 31, 2025  
**License:** MIT
