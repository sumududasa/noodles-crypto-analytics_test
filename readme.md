# Noodles Crypto Analytics Platform

**Industry Connect | Data Analytics Internship Portfolio Project**  
**End-to-end cryptocurrency social engagement analytics with Python, MySQL and Power BI**

![Python](https://img.shields.io/badge/Python-Data%20Processing-3776AB) ![MySQL](https://img.shields.io/badge/MySQL-Data%20Warehouse-4479A1) ![Power%20BI](https://img.shields.io/badge/Power%20BI-Interactive%20Reporting-F2C811)

> **Project demonstration:** [Watch the demo presentation](https://youtu.be/YR05MyXrXY4)  
> **Presentation slides:** [View the PowerPoint](docs/demo-presentation.pptx)

## Project overview

Noodles Crypto Analytics is an end-to-end analytics project that transforms JSON-based cryptocurrency metadata and Twitter/X and Reddit engagement records into a structured MySQL data warehouse and interactive Power BI reports.

The project demonstrates practical data preparation, data modelling, SQL aggregation, DAX measures, reporting, validation and documentation. Its primary focus is **historical social engagement**, not live trading signals or price prediction.

## Business problem

Source data arrived in multiple JSON files with different structures and field names. Without standardisation and a common reporting model, it is difficult to compare tokens, track engagement over time and understand differences between social platforms.

**Project objectives:**

- Prepare and standardise cryptocurrency and social engagement data using Python and pandas.
- Store analytics-ready records in a relational MySQL star schema.
- Build reusable aggregation tables and SQL reporting views.
- Deliver interactive Power BI reports for non-technical stakeholders.
- Validate the data and document how the solution can be operated and maintained.

## Architecture

```text
JSON source files
       |
       v
Python + pandas / Jupyter notebooks
  - Extraction and transformation
  - Normalisation and validation
       |
       v
MySQL: noodles_dw
  - DimCurrency / DimDate / DimPlatform
  - FactSocialEngagement
       |
       v
Aggregations and SQL reporting views
       |
       v
Power BI Desktop
  - Top Performers report (Task 7)
  - Executive Dashboard (Task 8)
```

[View the architecture diagram](docs/architecture-diagram.png) *(add the PNG to `docs/` before publishing)*.

### Technology stack

| Layer | Technologies |
|---|---|
| Source data | JSON; cryptocurrency metadata; Twitter/X and Reddit engagement |
| Data processing | Python, pandas, Jupyter Notebook |
| Database access | SQLAlchemy, PyMySQL |
| Data warehouse | MySQL (`noodles_dw`), star schema |
| Reporting preparation | SQL views, Python-generated summary tables |
| Business intelligence | Microsoft Power BI Desktop, DAX, Power Query / ODBC connection |
| Documentation | Markdown, Excel, PowerPoint |

## Data sources

The project uses these four source file types:

| Source file | Content |
|---|---|
| `v2_token.json` | Cryptocurrency reference information |
| `v2_token_overview.json` | Cryptocurrency overview / market metadata |
| `v4_x_tweets.json` | Twitter/X engagement records |
| `v5_reddit_post_engagement.json` | Reddit engagement records |

The original JSON datasets may need to be obtained separately if they are not included in this public repository. Consult the data dictionary for details; verify source row counts against the working files.

## Data warehouse and reporting model

**Database:** `noodles_dw`

| Warehouse table | Purpose |
|---|---|
| `DimCurrency` | Token identifiers and currency attributes |
| `DimDate` | Calendar dates and date attributes |
| `DimPlatform` | Social platform identifiers |
| `FactSocialEngagement` | Post-level engagement metrics and dimension keys |

**Reporting objects** prepared for Power BI include:

- Aggregation tables: `CurrencySummary`, `DailySocialSummary`, `SocialEngagementSummary` and an enriched currency summary.
- SQL views: `vw_ExecutiveDashboard`, `vw_TimeSeries`, `vw_SocialAnalytics` and `vw_PlatformDaily`.

The Task 5 schema export recorded **2,682 social engagement fact rows** at the time of that export. Counts should be rechecked in the live database after any refresh.

For table-level definitions, see the [data dictionary](docs/data-dictionary.xlsx).

## Power BI reports

### Task 7 — Top Performers

**Report:** `reports/NoodlesCrypto_TopPerformers.pbix`

Provides a ranked view of cryptocurrency engagement and supporting performance metrics for analysis.

### Task 8 — Executive Dashboard

**Report:** `reports/NoodlesCrypto_ExecutiveDashboard.pbix`

The report includes three main pages:

1. **Executive Overview:** Total Engagements, Average Engagement Score, Active Tokens, engagement trends, ranked tokens, dynamic Top N selection, platform engagement share and engagement quality comparison.
2. **Platform Performance Analysis:** Twitter/X and Reddit engagement trends and platform-level metrics.
3. **Token Drill-through:** A detail page intended to show selected-token information and platform breakdowns.

Additional interactive features include date slicers, DAX measures, cross-filtering, bookmark navigation and tooltips. **Verify that drill-through filters the detail visuals correctly and that bookmarks change the intended view before demonstrating these features as fully operational.**

An example Task 8 dashboard state showed **2,682 Total Engagements**, **25.99 Average Engagement Score** and **33 Active Tokens**. These values depend on the data and current filters; they are not guaranteed to remain unchanged.

### Dashboard screenshots

Add your actual exported screenshots to the repository, then update these paths if your filenames differ:

- [Executive Overview screenshot](reports/screenshots/executive_overview.png)
- [Platform Performance screenshot](reports/screenshots/platform_analysis.png)
- [Token Drill-through screenshot](reports/screenshots/token_drillthrough.png)

## How to run the project

### Prerequisites

- Python and Jupyter Notebook (or Anaconda)
- MySQL Server with access to the project database
- Power BI Desktop on Windows
- Python dependencies used in the notebooks
- Original input datasets and the warehouse-building notebook if rebuilding from scratch

### Setup and execution

1. Clone or download this repository.
2. Install Python dependencies in your chosen environment:

   ```bash
   python -m pip install pandas numpy sqlalchemy pymysql python-dotenv jupyter matplotlib seaborn
   ```

3. Configure your MySQL credentials in a **local `.env` file**, excluded from Git. Do not commit passwords or connection secrets.
4. Prepare/load the MySQL star schema using the Task 5 warehouse-building workflow and the original source data.
5. Open and execute the Task 6 notebook (`06_powerbi_prep.ipynb`; the archived copy may have a duplicated `.ipynb` extension) to generate summary tables and reporting views.
6. Run the notebook's validation checks and confirm that the expected tables and views contain data.
7. Open the Task 7 or Task 8 `.pbix` file in Power BI Desktop.
8. If refreshing the report, ensure its MySQL/ODBC connection and credentials point to your available database; select **Home → Refresh**.

**Important:** The archived Task 1–8 submission packages did not include every source file or the executable Task 5 warehouse-loading notebook. A full rebuild requires those files from the original working project. Scheduled ETL or Power BI Service refresh has not been verified.

For detailed setup, validation, recovery and troubleshooting instructions, see the [technical runbook](docs/technical-runbook.md).

## Data quality and limitations

- Review missing values, data types, duplicate records, row counts and warehouse relationships before publishing refreshed results.
- Verify social platform coverage and the date range of reporting views.
- Treat engagement score as an engagement measure, **not** proof of positive sentiment or investment performance.
- A warehouse load timestamp is not necessarily the original post date; use the appropriate reporting date for trend analysis.
- Some aggregate views do not have token-level identifiers; validate drill-through and filter propagation rather than assuming all visuals are token-specific.
- Do not claim automated daily scheduling, Power BI Service publication, refresh performance or data-quality percentages without direct evidence.

## Documentation and deliverables

| Deliverable | Location |
|---|---|
| Architecture diagram | [docs/architecture-diagram.png](docs/architecture-diagram.png) |
| Data dictionary | [docs/data-dictionary.xlsx](docs/data-dictionary.xlsx) |
| Technical runbook | [docs/technical-runbook.md](docs/technical-runbook.md) |
| Stakeholder user guide | [docs/user-guide.md](docs/user-guide.md) |
| Demo presentation | [docs/demo-presentation.pptx](docs/demo-presentation.pptx) |
| Demo video | [Watch the recorded demo](YOUR_VIDEO_URL_HERE) |
| Final project checklist | [docs/final-checklist.md](docs/final-checklist.md) |
| Task 8 dashboard guide | [reports/executive-dashboard-guide.md](reports/executive-dashboard-guide.md) |
| Task 8 DAX reference | [reports/dax-measures-reference.md](reports/dax-measures-reference.md) |

**Before publishing:** Confirm every linked file exists in the repository. Add the final checklist and the correct video URL; remove or update any links to files not yet committed.

## Project outcomes and skills demonstrated

- Designed and worked with a MySQL star schema for analytics.
- Transformed and validated JSON-based data using Python and pandas.
- Built SQL reporting views and aggregation structures for Power BI.
- Created Power BI KPI cards, time-series visuals, ranked token tables and platform comparisons.
- Applied DAX, dynamic Top N and interactive report features.
- Prepared technical and stakeholder-facing documentation for handover.

## Demo and presentation

- **Video:** [Noodles Crypto Analytics — Final Demonstration](YOUR_VIDEO_URL_HERE)
- **Slides:** [Download the presentation](docs/demo-presentation.pptx)

## Author

**Sumudu Dasanayaka**  
Industry Connect — Data Analytics Internship Project  
Auckland, New Zealand

---

*Portfolio project for educational and analytical purposes. This dashboard does not provide financial advice.*
