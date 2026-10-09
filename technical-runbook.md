# Technical Runbook — Noodles Crypto Analytics Platform

**Project:** Industry Connect, Task 9  
**Scope:** Python/Jupyter analytics, MySQL `noodles_dw`, Power BI reporting  
**Status:** Documentation derived from uploaded Task 1–8 submission archives; steps that require access to the live environment are marked **Verify**.

## 1. Purpose and system overview

This runbook helps a developer or analyst operate, validate, refresh, troubleshoot, and recover the Noodles Crypto Analytics solution.

**Observed pipeline:** JSON currency and social-media data → Python/pandas transformations and analyses → MySQL star-schema warehouse → SQL/Python aggregation and reporting views → Power BI Desktop reports.

The source data covers currency metadata and Twitter/X and Reddit engagement. The Power BI reports focus on **social engagement**, rather than claiming live market prices or investment predictions.

### Confirmed technologies and artifacts

| Component | Implementation or evidence |
|---|---|
| Data processing | Python, pandas; Jupyter notebooks |
| Database | MySQL, database `noodles_dw` |
| Database access | SQLAlchemy with PyMySQL; environment variables supported in Task 6 notebook |
| Validation | SQL checks, notebook QA cells, CSV reject output |
| Power BI preparation | `06_powerbi_prep.ipynb.ipynb` (actual uploaded filename; rename to `06_powerbi_prep.ipynb` only if desired and tested) |
| Task 4 analysis | `04_combined_dashboard.ipynb` |
| Task 5 schema | `data_warehouse_schema.md` (Task 5 archive) |
| Task 7 report | `NoodlesCrypto_TopPerformers.pbix` |
| Task 8 report | `NoodlesCrypto_ExecutiveDashboard.pbix` |

**Not included in the uploaded archives:** the Task 5 executable warehouse-loading notebook, original JSON source files, `.env`, full SQL deployment scripts, or a configured Power BI Service refresh. Obtain these from the working project before attempting a complete rebuild. Screenshots alone are not executable backups.

## 2. Architecture and data model

### Data flow

1. **Source:** currency metadata and overview JSON, Twitter/X engagement JSON, Reddit engagement JSON.
2. **Transformation:** Python/pandas normalization, calculations and quality checks.
3. **Core warehouse:** MySQL `noodles_dw` with currency, date, platform dimensions and social engagement fact.
4. **Aggregation:** currency, daily, and social summary tables, enriched calculations, reporting views.
5. **Presentation:** Power BI Top Performers and Executive Dashboard reports.
6. **Control:** Jupyter notebook execution, console logging and notebook validation cells; automated scheduling has **not** been verified.

### Core warehouse objects (from Task 5 schema)

| Table | Key fields / grain |
|---|---|
| `DimCurrency` | `CurrencyKey` PK; `Symbol`, `CurrencyName`, `BaseCurrency`, `Website`, `CirculatingSupply`, `MaxSupply`, `LoadDate` |
| `DimDate` | `DateKey` PK; `FullDate`, `Year`, `Quarter`, `Month`, `Week`, `MonthName`, `DayOfMonth`, `DayOfWeek`, `DayName`, `IsWeekend` |
| `DimPlatform` | `PlatformKey` PK; `PlatformName`, `PlatformDescription` |
| `FactSocialEngagement` | `EngagementKey` PK; foreign keys to three dimensions; `PostId`, `Likes`, `Retweets`, `Comments`, `Impressions`, `EngagementScore`, `LoadDate` |

**Fact grain:** one row per social media post/engagement record. Task 5 schema reports **2,682 fact rows** at the time of that export. Recheck the live count after refresh; this is not a performance benchmark.

### Task 6 derived objects

- Tables: `CurrencySummary`, `DailySocialSummary`, `SocialEngagementSummary`; `CurrencySummary_Enriched` is written by the calculated-category function. Inspect the database for any other enriched tables.
- Reporting views: `vw_ExecutiveDashboard`, `vw_TimeSeries`, `vw_SocialAnalytics`, `vw_PlatformDaily`.
- Important semantics: `LastEngagementDate` in the currency summary is based on `MAX(f.LoadDate)` in the uploaded notebook, **not necessarily the social post date**. Use `DimDate.FullDate`/view `FullDate` for time-series analysis.

## 3. Prerequisites and initial setup

1. Install and start MySQL; ensure the `noodles_dw` database and Task 5 tables already exist.
2. Install Python and Jupyter (or use the existing working Python environment).
3. Install notebook dependencies in the selected environment:

   ```powershell
   python -m pip install pandas numpy sqlalchemy pymysql python-dotenv jupyter matplotlib seaborn
   ```

4. Keep database credentials outside source code. The uploaded Task 6 notebook contains a hard-coded database password/default. **Rotate that credential**, remove the literal from the notebook, and use a local `.env` file excluded from Git.
5. Create a local `.env` file in the notebook's working directory (replace placeholders with real values):

   ```dotenv
   DB_USER=<mysql_user>
   DB_PASSWORD=<mysql_password>
   DB_HOST=localhost
   DB_PORT=3306
   DB_NAME=noodles_dw
   ```

6. Ensure `.gitignore` includes `.env`, `*.log`, and any sensitive source exports.
7. Confirm the Task 5 warehouse notebook and original data files are available in the working project if a full rebuild is required. Their executable contents were **not** included in the uploaded Task 5 ZIP.

### Connection smoke test

Run in Jupyter using the configured environment:

```python
from dotenv import load_dotenv
from sqlalchemy import create_engine, URL, text
import os
load_dotenv()
url = URL.create(
    'mysql+pymysql',
    username=os.environ['DB_USER'],
    password=os.environ['DB_PASSWORD'],
    host=os.getenv('DB_HOST', 'localhost'),
    port=int(os.getenv('DB_PORT', '3306')),
    database=os.getenv('DB_NAME', 'noodles_dw'),
)
engine = create_engine(url, pool_pre_ping=True)
with engine.connect() as conn:
    print(conn.execute(text('SELECT DATABASE()')).scalar())
```

Expected database name: `noodles_dw`. Do not print or commit the password.

## 4. Manual operating procedure

**This is the supported procedure based on the submitted notebook; an unattended daily job is not evidenced.**

### A. Confirm warehouse readiness (Task 5)

1. Start MySQL and open MySQL Workbench or Jupyter.
2. Confirm `DimCurrency`, `DimDate`, `DimPlatform`, `FactSocialEngagement` exist.
3. If missing, restore the database or run the original **Task 5 warehouse-loading notebook from the working project**; it was not present in the uploaded Task 5 completion ZIP.
4. Do not execute Task 6 against an empty warehouse.

### B. Refresh reporting tables/views (Task 6)

1. Back up `noodles_dw` before any refresh (see §9). Task 6 aggregation functions use `DROP TABLE IF EXISTS` and rebuild tables; the enriched table uses `to_sql(..., if_exists='replace')`.
2. Open `task6_completion/06_powerbi_prep.ipynb.ipynb` in Jupyter, or its corresponding working-project copy.
3. Confirm the notebook kernel has the required packages and safe `.env` settings.
4. Run notebook cells **in order**. The uploaded notebook contains:

   | Notebook cell index (0-based) | Function |
   |---|---|
   | 0 | Direct connection example containing hard-coded credentials — **replace/remove before running** |
   | 1 | `.env`-aware `get_engine()` and table-name helper |
   | 2 | `setup_logger()` (console `StreamHandler`) |
   | 3 | Calculated/enriched column functions |
   | 4 | Aggregation table creation functions |
   | 5 | Execute aggregation table creation |
   | 6 | Execute calculated/enriched outputs |
   | 7 | Create reporting views |
   | 8 | Validate aggregation and view row counts |
   | 9 | Additional aggregation QA/plots |
   | 10 | Reject checks and CSV output |
   | 11 | Sample rows from four reporting views |

5. Confirm the notebook prints successful aggregation and view counts. Do not assume a success message proves every quality check passed; review QA output.
6. If a cell fails, stop and resolve the error before refreshing Power BI.

**Notebook execution note:** The cell indices above are based on the uploaded file, not a promise that your working copy has identical numbering. Notebook order matters because functions and `engine` are defined in earlier cells.

### C. Refresh Power BI Desktop

1. Open `task8_completion/Reports/NoodlesCrypto_ExecutiveDashboard.pbix` or the working copy under `reports/`.
2. Verify the MySQL ODBC/connector data source points to the intended `noodles_dw` instance; the user previously used an ODBC source named `NoodlesDW`.
3. In **Home → Transform data → Data source settings**, correct the connection if needed. Avoid embedding credentials in documentation.
4. Click **Home → Refresh** and wait for completion; duration depends on environment and is **not benchmarked** in the uploaded evidence.
5. Check Executive Overview, Platform Performance Analysis and Token Drill-through pages for visual errors.
6. Test the date slicer, dynamic Top N, bookmarks, and drill-through. In particular, confirm the token-specific breakdown actually changes between different tokens; a global daily view may not support token-level time-series filtering.
7. Save the `.pbix` file after validation.

### D. Task 7 report

Open `task7_completion/Reports/NoodlesCrypto_TopPerformers.pbix` and refresh/test its Executive Dashboard, Time Series Analysis, Platform Analysis and Currency Deep Dive pages as needed.

## 5. Monitoring and data quality

The uploaded Task 6 notebook includes console logging, table/view counts, aggregation comparison, and checks for orphan currency keys, null metrics and duplicate platform/post IDs. It creates a timestamped CSV under `data/rejects/` when problem rows are found; inspect this file if present. The notebook's logger uses a **console handler**; a persistent `logs/etl_*.log` file is **not demonstrated** by the uploaded code.

### SQL verification queries

Run in MySQL Workbench after refreshing:

```sql
USE noodles_dw;

-- Confirm core and reporting objects
SHOW FULL TABLES;

-- Core fact volume (compare to prior runs)
SELECT COUNT(*) AS fact_rows FROM FactSocialEngagement;

-- Referential integrity: expect 0 rows in each count
SELECT COUNT(*) AS orphan_currency_facts
FROM FactSocialEngagement f
LEFT JOIN DimCurrency d ON d.CurrencyKey = f.CurrencyKey
WHERE d.CurrencyKey IS NULL;

SELECT COUNT(*) AS orphan_date_facts
FROM FactSocialEngagement f
LEFT JOIN DimDate d ON d.DateKey = f.DateKey
WHERE d.DateKey IS NULL;

SELECT COUNT(*) AS orphan_platform_facts
FROM FactSocialEngagement f
LEFT JOIN DimPlatform d ON d.PlatformKey = f.PlatformKey
WHERE d.PlatformKey IS NULL;

-- Duplicate post identifiers within the same platform
SELECT PlatformKey, PostId, COUNT(*) AS duplicate_count
FROM FactSocialEngagement
GROUP BY PlatformKey, PostId
HAVING COUNT(*) > 1;

-- Missing critical engagement values
SELECT COUNT(*) AS null_metric_rows
FROM FactSocialEngagement
WHERE Likes IS NULL OR EngagementScore IS NULL;

-- Reporting view volumes
SELECT COUNT(*) AS executive_rows FROM vw_ExecutiveDashboard;
SELECT COUNT(*) AS time_series_rows FROM vw_TimeSeries;
SELECT COUNT(*) AS social_rows FROM vw_SocialAnalytics;
SELECT COUNT(*) AS platform_daily_rows FROM vw_PlatformDaily;

-- Check platform and calendar coverage
SELECT p.PlatformName, COUNT(*) AS post_rows,
       SUM(f.Likes) AS total_likes,
       AVG(f.EngagementScore) AS avg_score,
       COUNT(DISTINCT d.FullDate) AS distinct_dates
FROM FactSocialEngagement f
JOIN DimPlatform p ON p.PlatformKey = f.PlatformKey
JOIN DimDate d ON d.DateKey = f.DateKey
GROUP BY p.PlatformName;
```

**Acceptance guidance:** No orphaned keys, unexpected duplicates or missing required metrics; populated reporting views; plausible platform counts and dates. A zero-row reject report is good, but investigate unexpected data drops. Do not claim a specific data-quality percentage without calculating it.

## 6. Troubleshooting

| Symptom | Likely cause | Resolution |
|---|---|---|
| `ModuleNotFoundError` | Wrong Python environment or missing package | Select the intended Jupyter kernel; install missing package with `python -m pip install ...` |
| MySQL `OperationalError` / access denied | MySQL stopped, wrong host/port, invalid credentials | Start MySQL, check `.env`, confirm `noodles_dw`, rerun connection smoke test |
| Missing `FactSocialEngagement` | Warehouse not loaded | Run/restore Task 5 first; do not continue Task 6 |
| `NameError: engine` or missing function | Cells executed out of order | Restart kernel and run cells sequentially after fixing credentials |
| Missing aggregation table | Prior creation step failed | Inspect first failing cell; back up and rerun aggregation creation |
| Missing `vw_*` view | View creation failed | Check table dependencies, rerun view creation cell, then count rows |
| Unexpected row counts | Source/warehouse mismatch or duplicate data | Compare fact counts, summary counts, and date/platform breakdowns |
| Reject CSV contains rows | Null metrics, duplicate post IDs, orphan currency | Inspect `reject_reason`, trace upstream source, correct and rerun |
| Power BI credential / ODBC error | Driver, DSN or data source settings incorrect | Verify driver and DSN; update Power BI data source settings |
| Power BI visual shows no data | Date filter excludes records or relationships/field names mismatch | Reset slicers, inspect model relationships, verify SQL view data |
| Drill-through shows global totals | Drill-through key does not propagate through dimension relationships | Verify source/drill-through fields use a compatible currency dimension and retest |
| Time trend appears wrong | Load timestamp used instead of event date or missing date grain | Use `FullDate` from appropriate daily view and verify source grain |

## 7. Power BI report operating checks

### Task 7 — Top Performers

Uploaded report guide documents four pages: Executive Dashboard, Time Series Analysis, Platform Analysis, Currency Deep Dive. It uses reporting views such as `vw_ExecutiveDashboard` and social engagement measures.

### Task 8 — Executive Dashboard

Uploaded `.pbix` and screenshots show three report pages: Executive Overview, Platform Performance Analysis, Token Drill-through. The accompanying DAX file documents:

- `Total Engagements`
- `Average Engagement Score`
- `Active Tokens`
- `Platform Total Engagements`
- `Platform Avg Engagement Score`
- `Platform Engagement Share %`
- `Token Engagement Rank`
- `Show Top N Token` (depends on `[Top N Count Value]`)

Check the live PBIX model for any additional measures. The DAX text file is not a guaranteed complete inventory of measures embedded in the report.

**Important:** `Total Engagements` in the Executive Dashboard sums `vw_ExecutiveDashboard[TotalEngagements]` (a count of social engagement records in the currency summary). It is not interchangeable with summed likes/comments/retweets or market trading volume.

## 8. Refresh frequency and scheduling

**Verified:** manual notebook execution and Power BI Desktop refresh procedure.  
**Not verified:** production cron/Windows Task Scheduler, automated alerts, on-premises data gateway, Power BI Service publication, or a fixed 6 AM / 7 AM schedule.

If automated execution is introduced later, document the actual command, environment, dependencies, logs, schedule, notification path and service credentials **after testing**. Do not mark automation as operational based solely on a template.

## 9. Backup and recovery

### MySQL backup (MySQL syntax)

Run from a secure terminal with `mysqldump` installed; do not put a plaintext password in the command:

```bash
mysqldump -u YOUR_MYSQL_USER -p --single-transaction noodles_dw > noodles_dw_backup.sql
```

The command prompts for the password. Store backups outside public Git repositories and verify that the output file is nonempty.

### MySQL restore (destructive — use only when appropriate)

Before restoring, confirm the target database, back up its current state, and obtain approval if other users rely on it:

```bash
mysql -u YOUR_MYSQL_USER -p noodles_dw < noodles_dw_backup.sql
```

### Code and report backups

- Commit notebooks, SQL scripts, Markdown documentation and non-sensitive assets to Git.
- Keep original JSON datasets in a secure location; include only if redistribution is permitted.
- Back up `.pbix` files before structural changes.
- Keep `.env`, database dumps containing sensitive information, and credentials out of public repositories.
- After recovery, rerun SQL QA, rebuild derived objects if required, refresh Power BI and retest interactions.

## 10. Repository locations and ownership

The following paths describe the **recommended consolidated repository layout**; the uploaded materials arrived as separate `taskN_completion/` ZIP archives rather than one fully verified repository tree.

```text
project-root/
├── task1_completion/ ... task8_completion/   # submission evidence archives
├── 04_combined_dashboard.ipynb                # Task 4 notebook, if consolidated
├── 05_data_warehouse_design.ipynb            # retrieve from working project; not in ZIP
├── 06_powerbi_prep.ipynb                     # optional normalized name of uploaded Task 6 notebook
├── reports/
│   ├── NoodlesCrypto_TopPerformers.pbix
│   └── NoodlesCrypto_ExecutiveDashboard.pbix
├── docs/
│   ├── architecture-diagram.png
│   ├── data-dictionary.xlsx
│   ├── technical-runbook.md
│   └── user-guide.md
└── .env                                      # local only; never commit
```

**Maintainer:** project owner / Industry Connect candidate. **Database administrator and business owner:** not identified in the uploaded files. Add real contacts only when confirmed.

## 11. Handover / final validation checklist

- [ ] MySQL `noodles_dw` accessible using non-hard-coded credentials.
- [ ] Original source JSON and Task 5 loading notebook are available for full rebuild.
- [ ] Core dimensions and fact table exist; fact count recorded.
- [ ] Task 6 notebook completes without exceptions.
- [ ] Aggregation tables and all four `vw_*` views contain expected rows.
- [ ] Orphan, null and duplicate checks reviewed; reject files investigated.
- [ ] Power BI refresh completes without errors.
- [ ] Task 7 and Task 8 pages and interactions tested, including token drill-through.
- [ ] MySQL backup and report copies saved securely.
- [ ] README, architecture diagram, data dictionary, user guide and demo materials linked correctly.
- [ ] No hard-coded credentials or unsupported performance/automation claims in public materials.

## 12. Evidence and limitations

This runbook was prepared from the uploaded `task1_completion.zip` through `task8_completion.zip`, especially:

- `task4_completion/04_combined_dashboard.ipynb`
- `task5_completion/data_warehouse_schema.md`
- `task6_completion/06_powerbi_prep.ipynb.ipynb`
- `task7_completion/Docs/powerbi-report-guide.md`
- `task7_completion/Docs/dax-measures-list.md`
- `task7_completion/Reports/NoodlesCrypto_TopPerformers.pbix`
- `task8_completion/Doc/Dax_Measures.md`
- `task8_completion/Reports/NoodlesCrypto_ExecutiveDashboard.pbix`

The live MySQL database, source JSON, service settings, schedules, credentials, and full Task 5 executable notebook were not supplied; this document distinguishes their unverified status from the uploaded evidence. Before handing over, run the documented checks in the actual working environment and update any environment-specific paths.
