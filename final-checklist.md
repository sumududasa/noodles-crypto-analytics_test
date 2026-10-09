# Task 9 — Final Project Checklist

**Project:** Noodles Crypto Analytics Platform  
**Programme:** Industry Connect  
**Status:** Verification pending — tick an item only after checking it.

> This checklist follows the Task 9 instructions. An unchecked item does not necessarily mean the feature is broken; it means it has not yet been verified. Do not claim optional or unimplemented functionality as complete.

## 1. Code and data pipeline

- [ ] Original JSON input files are present at the documented locations (or accessible from an approved source).
- [ ] Required Python environment and dependencies are documented and install successfully.
- [ ] Actual Task 5 warehouse-building notebook or equivalent runnable implementation is present.
- [ ] Task 6 Power BI preparation notebook runs without errors against the intended database.
- [ ] SQL scripts and notebook paths in the technical runbook match the repository.
- [ ] No passwords, API keys, `.env` contents, or other secrets appear in committed notebooks, scripts, logs, or screenshots.
- [ ] Database credentials used in earlier development have been rotated if exposed.

## 2. MySQL data warehouse and quality

- [ ] `noodles_dw` is accessible and its schema is verified.
- [ ] `DimCurrency`, `DimDate`, `DimPlatform`, and `FactSocialEngagement` exist.
- [ ] Aggregation tables used in Task 6 exist and contain expected data.
- [ ] `vw_ExecutiveDashboard`, `vw_TimeSeries`, `vw_SocialAnalytics`, and `vw_PlatformDaily` return data.
- [ ] Actual row counts have been recorded in the data dictionary.
- [ ] Duplicate/null checks and key relationships have been validated.
- [ ] No orphaned fact records remain, or exceptions are explained.
- [ ] Twitter and Reddit engagement metrics and date coverage have been checked.
- [ ] Any data-quality percentage in the presentation/README is supported by an actual calculation.

## 3. Power BI reports

- [ ] `reports/NoodlesCrypto_TopPerformers.pbix` opens successfully.
- [ ] `reports/NoodlesCrypto_ExecutiveDashboard.pbix` opens successfully.
- [ ] Executive Overview displays KPI cards, engagement trend, and Top Tokens.
- [ ] Platform Performance Analysis displays platform metrics and a date-based trend.
- [ ] Token Drill-through opens from a selected token and filters relevant detail visuals correctly.
- [ ] Date slicers and cross-filter interactions work as documented.
- [ ] Dynamic Top N displays the correct number of tokens (test 5 and 10).
- [ ] Bookmark buttons change the intended report view, not merely their selected appearance.
- [ ] Tooltips show the intended values.
- [ ] Measure count and time-intelligence requirements from Task 8 are met or discrepancies are documented.
- [ ] No broken visuals or refresh errors appear in the final saved PBIX.
- [ ] Dashboard screenshots match the final report state.

## 4. Task 9 documentation

- [ ] `docs/architecture-diagram.png` exists and shows JSON → Python/pandas → MySQL star schema → aggregations/views → Power BI, with notebook orchestration/validation.
- [ ] `docs/data-dictionary.xlsx` contains all six required sheets: Source Files, Staging Tables, Dimensions, Facts, Aggregation Tables, Python Functions.
- [ ] Example counts and placeholder functions in the data dictionary have been replaced with verified project values or clearly labelled as not applicable.
- [ ] Data dictionary covers actual tables, columns, keys, and relevant Python functions.
- [ ] `docs/technical-runbook.md` explains setup, execution, refresh, validation, troubleshooting, backup, and recovery using actual tools and paths.
- [ ] `docs/user-guide.md` explains both Power BI reports, page navigation, slicers, Top N, drill-through, metrics, and troubleshooting.
- [ ] `docs/demo-presentation.pptx` opens and contains 10–12 professional slides.
- [ ] Presentation statements, screenshots, record counts, and performance claims have been verified.
- [ ] `docs/demo-video.mp4` is saved locally, or an accessible video link is documented.
- [ ] Video is approximately 10 minutes, audible, readable, and shows the pipeline/dashboard demonstration required by Task 9.
- [ ] `README.md` is in the repository root with project overview, architecture, setup, insights, and links.
- [ ] All README relative links and the demo video URL work after publishing.

## 5. GitHub and portfolio

- [ ] Repository structure is organised and matches the README.
- [ ] `.gitignore` excludes `.env`, secrets, local virtual environments, and unnecessary generated files.
- [ ] Repository contains no sensitive credentials or data that cannot be shared publicly.
- [ ] Public GitHub repository is available, if approved by the project owner.
- [ ] GitHub README displays correctly and documentation links open.
- [ ] Power BI files and demo video are hosted or linked appropriately if too large for ordinary GitHub upload.
- [ ] LinkedIn portfolio post has been drafted and, if appropriate, published.
- [ ] GitHub and demo links have been added to the final submission.

## 6. Final handover

- [ ] All deliverables from Tasks 1–8 are retained and organised.
- [ ] Task 9 required files are present under `docs/` and project root.
- [ ] A reviewer can follow the technical runbook to reproduce the pipeline with the required inputs and credentials.
- [ ] A non-technical reviewer can follow the user guide to navigate the Power BI reports.
- [ ] Any unfinished, optional, or unverified requirements are explicitly listed in submission notes.
- [ ] Final project has been submitted through the official Industry Connect submission process.

## Submission notes / exceptions

Record anything still pending, with a clear explanation and next action.

| Requirement | Status | Evidence or next action |
|---|---|---|
| Task 5 executable notebook and original JSON files | To verify | Confirm files are available in repository or approved storage |
| Drill-through token-specific filtering | To test | Compare two tokens and check detailed visuals change |
| Bookmarks | To test | Verify actual view/state changes |
| Automated refresh / Power BI Service | Not confirmed | Do not claim configured unless tested |
| Data dictionary counts and function names | To verify | Compare with database and notebooks |
| Public GitHub repository | Pending | Publish only after secret and sharing review |
| Demo video URL | Pending | Add actual YouTube/Loom link |

**Completion rule:** Mark the project ready only when every mandatory requirement is verified, or any exception has been explicitly accepted by your Industry Connect reviewer.
