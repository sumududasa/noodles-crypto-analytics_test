# User Guide — Noodles Crypto Analytics Dashboards

**Industry Connect | Task 9 | Stakeholder edition**  
**Reports:** Task 7 Top Performers and Task 8 Executive Dashboard  
**Audience:** executives, business managers, analysts, and other non-technical report users  
**Scope:** historical cryptocurrency **social-media engagement** (Twitter/X and Reddit). This is not a live trading or price-prediction service.

## 1. What this solution does

Noodles Crypto Analytics brings together cryptocurrency reference data and social engagement activity. Python/pandas prepares the data, MySQL `noodles_dw` stores the warehouse and reporting views, and Power BI presents interactive results. Use the reports to see which tokens receive the most engagement, how activity changes over time, and how Twitter/X compares with Reddit.

The reports measure engagement in the imported dataset. A higher engagement score is **not** proof of better investment returns, positive sentiment, or future price movement.

## 2. Open a report

### Option A — Power BI Desktop (confirmed deliverable)

1. Open **Power BI Desktop** on a Windows computer.
2. Select **File → Open → Browse** and choose one of these project files:
   - `reports/NoodlesCrypto_TopPerformers.pbix` (Task 7)
   - `reports/NoodlesCrypto_ExecutiveDashboard.pbix` (Task 8)
3. Wait for the report pages to load. Use the page tabs at the bottom to move between pages.
4. You can explore the existing imported data without changing the database. For fresh data, use **Home → Refresh** only if you have access to the configured MySQL data source and credentials.

**Power BI Service:** A published report, workspace, sharing permissions, gateway, and scheduled refresh were **not verified** in the supplied Task 1–8 archives. Do not assume a browser link or daily refresh exists. If your project administrator later publishes the report, ask for the actual workspace/report link and access permission.

## 3. Task 8 — Executive Dashboard

**File:** `NoodlesCrypto_ExecutiveDashboard.pbix`

### Page 1 — Executive Overview

Start here for a summary of cryptocurrency engagement.

| Element | What it tells you | How to use it |
|---|---|---|
| Total Engagements KPI | Total engagement value in the report's current filter context | Review the overall activity level |
| Average Engagement Score KPI | Average engagement score according to the report measure | Compare engagement quality across selections |
| Active Tokens KPI | Distinct tokens represented in the executive data | Understand coverage |
| Engagement trend line | Engagement over the available dates | Look for spikes or changes over time |
| Top Tokens by Engagement table | Token symbol, name, engagement, average score, and rank | Identify the leading tokens |
| Platform Engagement Share donut | Relative engagement split across platforms | Compare Twitter/X with Reddit |
| Engagement Quality by Platform columns | Average engagement score by platform | Compare quality scores |
| Top N Count selector | Number of ranked tokens displayed | Choose 5, 10, 15, or 20 when offered |

**Illustrative report state from Task 8 work:** Total Engagements **2,682**, Average Engagement Score **25.99**, Active Tokens **33**. These are not fixed business targets; values can differ after refresh or filter changes. Check the currently displayed cards rather than relying on this example.

#### Change the Top N list

1. On **Executive Overview**, find the **Top N Count** slicer.
2. Select **5** to show the top five tokens, or **10**, **15**, or **20** for a longer list.
3. Check the **Top Tokens by Engagement** table. Its rank and engagement values determine the displayed order.
4. If the table looks unchanged, clear other filters and confirm the Top N visual filter is active.

#### Use the bookmark navigator

The page includes **Overview**, **Top Engagement**, and **Platform Comparison** navigation labels. Select a label to move to the saved view **if its bookmark has been configured with a distinct display state**. In Power BI Desktop edit mode, **Ctrl+click** may be needed to activate a button. A highlighted label alone does not prove that the visuals changed; check the page content.

### Page 2 — Platform Performance Analysis

Use this page to compare engagement activity on **Twitter/X and Reddit**.

- **Daily platform trend line:** Shows activity by `FullDate` and `PlatformName`, sourced from `vw_PlatformDaily`. Hover over a point for its displayed value.
- **Platform metrics table:** Summarises platform/token engagement, likes, retweets, and average score from `vw_SocialAnalytics`. Depending on the report version, comments and last-engagement information may also appear.
- **Date range slicer:** Choose the start and end dates to inspect a period of interest.

**How to compare platforms:** Choose a date range that contains data for both platforms; inspect the line legend and metrics table. If a platform disappears, its records may fall outside the chosen range or another filter may be active. Do not interpret a missing line as proof of zero historical activity.

### Page 3 — Token Drill-through

This page is intended for closer inspection of a selected cryptocurrency token. It includes a **back button**, engagement trend, platform split donut, and platform breakdown table.

**How to open it:**

1. Go to **Executive Overview**.
2. In the Top Tokens table, **right-click a token row or symbol**.
3. Choose **Drill through → Token Drill-through** if the option appears.
4. Inspect the destination page and its filter context. Use the **back arrow** to return.

**Important validation note:** The supplied reporting model includes aggregate daily views that may not contain a token identifier. Therefore, a date-trend chart can show **overall** activity even when a token is selected. Also, filtering `vw_ExecutiveDashboard[CurrencySymbol]` does not automatically guarantee that `vw_SocialAnalytics` is filtered through a single-direction star-schema relationship. **Do not label a chart or platform breakdown as token-specific until you confirm its numbers change appropriately when drilling into two different tokens.** If they do not, ask the report developer to correct the drill-through field/model or label the visual as overall platform activity.

## 4. Task 7 — Top Performers Report

**File:** `NoodlesCrypto_TopPerformers.pbix`

The Task 7 documentation describes **four pages**:

| Page | Purpose | Typical visuals |
|---|---|---|
| Executive Dashboard | High-level token engagement | Top-token table and bar chart, KPI cards |
| Time Series Analysis | Engagement trends | Line chart, social activity area chart, engagement composition |
| Platform Analysis | Compare social platforms | Platform metrics table, comparison bar/column charts |
| Currency Deep Dive | Explore currency engagement | Currency selector, engagement trend, platform split |

On the **Executive Dashboard**, sort the token table by total engagements to identify leaders. On **Time Series Analysis**, move across dates and hover over chart points. On **Platform Analysis**, compare the platform totals and average scores. On **Currency Deep Dive**, use the currency selector where available and check which visuals respond to it.

Task 7 and Task 8 are separate `.pbix` files. The page names and controls are not necessarily identical.

## 5. Everyday interactions

### Filter dates

1. Find the **date range slicer**.
2. Select or type a start date and end date (the Task 8 slicer uses **Between**).
3. Review the affected visuals.
4. Clear the slicer to restore the full available range. Some pages may share a synchronised date selection.

**Note:** Not all views necessarily use the same date grain or relationships. If a KPI does not change after changing the date, it may be sourced from a view not related to the date dimension. This should be validated by the report owner rather than interpreted as unchanged activity.

### Cross-filter a visual

Click a bar, table row, or donut segment to filter/highlight other compatible visuals. Click the selection again, or use blank space, to clear it. Interactions depend on how the report author configured them.

### Read tooltips

Hover over a chart mark or donut segment. Power BI may show engagement totals, average scores, or platform share percentages. Tooltips describe the data point currently under the cursor; they do not change the report.

### Restore a clean view

Clear selected slicers and chart selections. In Power BI Desktop, use the report's reset control if one exists; otherwise reopen the saved report **without saving your exploratory changes** to return to its last saved state.

### Export information

If enabled, select the **ellipsis (…)** on a visual and choose **Export data**. The available export options depend on the visual, Desktop/Service environment, and permissions. Do not assume JSON export is available. For a shareable report document, check **File → Export** options in your installed Power BI version.

## 6. Metric definitions

| Metric | Plain-English meaning | Interpretation |
|---|---|---|
| Total Engagements | Report-defined total engagement across records/tokens | Activity measure, not unique people |
| Total Likes | Sum of likes in the relevant dataset | One component of engagement |
| Total Comments | Sum of comments | Discussion activity |
| Total Retweets | Sum of retweets/reposts where recorded | Redistribution activity; may be zero/not applicable for Reddit |
| Average Engagement Score | Mean of the model's `AvgEngagementScore` values in context | A computed score; do not assume it is a percentage |
| Active Tokens | Distinct count of token symbols in the executive view | Data coverage, not exchange listing status |
| Token Engagement Rank | Rank by total engagements, descending | Rank can change with report context |
| Platform Engagement Share % | Platform total engagements divided by the total across platform categories | Relative platform contribution |
| Top N | User-selected number of highest-ranked tokens | Controls how many rows are shown |

Some measures average **pre-aggregated scores**, so the displayed average may differ from an event-weighted average. Use the measure definition in the DAX reference when exact calculation details matter.

## 7. Worked stakeholder examples

**Question: Which tokens have the most social engagement?** Open Task 8 → **Executive Overview**, select **Top N = 10**, then review the ranked token table. Note the selected date/filter context before quoting results.

**Question: Is Reddit or Twitter more active?** Open **Platform Performance Analysis**. Use a date range with data for both, then compare the trend and platform metrics. Cross-check against the Executive Overview donut.

**Question: What is happening with one token?** Right-click the token in the Executive Overview table and choose drill-through. Check the selected-token filter and confirm that each destination visual actually responds to that token before drawing token-specific conclusions.

## 8. Data freshness, limitations, and responsible use

- This is an analytics project using loaded JSON and warehouse data; **live/real-time data is not established**.
- Data is updated only when the ETL/aggregation notebooks and Power BI refresh are successfully run.
- Power BI Service publication and scheduled refresh were **not evidenced** in the supplied archives.
- Date coverage can differ between Twitter/X and Reddit.
- A view named `LastEngagement` may represent a load/update timestamp rather than the original post date; confirm the field definition before using it for historical conclusions.
- Missing values, platform differences, duplicate symbols, and aggregation choices can affect interpretation.
- This report does not offer financial advice, price forecasts, or verified sentiment predictions.

## 9. Common questions and troubleshooting

**Why does my number differ from a screenshot?** Check filters, selected Top N, refresh state, and whether you opened Task 7 or Task 8. Screenshots reflect a particular saved data state.

**Why is a platform missing?** Widen the date range and clear other selections. Check whether records for that platform exist in the chosen period.

**Why doesn't the date slicer change every visual?** Some reporting views may lack a direct date relationship or use a different aggregation grain. Contact the report author to validate the model.

**Why does drill-through show the same platform totals for different tokens?** The source filter may not propagate to the social analytics view. Do not interpret these as token-specific until fixed and tested.

**Why can't I refresh?** You may not have the MySQL ODBC driver, `NoodlesDW` DSN, network access, or valid credentials. Ask the project maintainer; never place passwords in screenshots or shared documentation.

**Why can't I open the PBIX?** Install/update Power BI Desktop and check the file path. A `.pbix` is not an Excel file.

**Can I share this report online?** Only if it has been published to a workspace and you have the appropriate permissions/licensing. The submitted archives do not establish that publication has occurred.

## 10. Glossary

- **ETL:** Extract, Transform, Load — the process that prepares source data for analysis.
- **KPI:** Key Performance Indicator — a summary metric, often shown on a card.
- **DAX:** Power BI's calculation language for measures.
- **Slicer:** A report control for choosing filter values.
- **Drill-through:** Opening a detail page with a selected item as context.
- **Bookmark:** A saved report display or interaction state.
- **Data warehouse:** The MySQL database that stores cleaned, structured analytics data.
- **Reporting view:** A SQL query exposed as a reusable data source for Power BI.

## 11. Support and related project files

For report issues, contact the **Industry Connect project maintainer or report author**. No real support email, telephone number, or service desk has been supplied; do not use the sample contacts in the Task 9 brief.

- Technical operations: `docs/technical-runbook.md`
- Data definitions: `docs/data-dictionary.xlsx`
- Task 7 report documentation: `task7_completion/Docs/powerbi-report-guide.md` (inside submitted archive)
- Task 7 DAX documentation: `task7_completion/Docs/dax-measures-list.md` (inside submitted archive)
- Task 8 DAX documentation: `task8_completion/Doc/Dax_Measures.md` (inside submitted archive)
- Reports: `reports/NoodlesCrypto_TopPerformers.pbix`, `reports/NoodlesCrypto_ExecutiveDashboard.pbix`

## 12. Final checks before sharing with stakeholders

- [ ] Both PBIX files open without errors.
- [ ] Page titles, slicers, and metric labels match this guide.
- [ ] Top N selections show the expected number of rows.
- [ ] Date filtering is tested on each relevant visual.
- [ ] Bookmarks produce distinct intended views.
- [ ] Drill-through is tested using at least two different tokens; unsupported token-specific visuals are corrected or relabelled.
- [ ] Twitter/X and Reddit are both represented where the chosen date range contains data.
- [ ] Tooltip information appears where expected.
- [ ] Refresh process and data timestamp are confirmed before communicating current figures.
- [ ] Screenshots contain no credentials or private connection details.

---

*Prepared for Industry Connect Task 9 from the submitted Task 7 and Task 8 report artifacts, Task 7 report guide, Task 8 DAX list, and prior Task 9 technical runbook. Functional behaviours requiring the live Power BI environment are described as checks rather than asserted as verified.*
