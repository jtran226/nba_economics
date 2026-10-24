# NBA Economics — Data Sources & Methodology

> **Project phase:** Source ingestion and staging largely complete; canonical data modeling in progress.
>
> **Scope:** Historical NBA data, approximately 2000–01 through 2025–26, with coverage varying by source.

## 1. Overview

Briefly describe the purpose of the data pipeline and how the datasets will support the NBA economics project.

**Pipeline:** Raw source data → Cleaning and standardization → Staged source tables → Canonical data model → Analysis

This document focuses on **source acquisition, staging transformations, data quality, and limitations**. Canonical modeling and analytical methodology will be documented as those stages are completed.

## 2. Source Inventory & Coverage

Summarize the major source-table groups and their corresponding extraction methods.

| Source tables | Subject | Primary sources / methods |
|---|---|---|
| S01–S03 | Regular season standings, team performance via advanced stats, playoff results | Basketball-Reference; HTML table extraction |
| S04–S06 | Rosters and player statistics | NBA API wrapper |
| S07 | Player salary history | ESPN; paginated extraction |
| S08–S11 | Payroll, salary cap, financial thresholds | Spotrac, RealGM, historical reference tables, some manual extraction |
| S13 | CBA history and regime mapping | Historical CBA reference data, some manual extraction |
| S14A–S14C | All-NBA, awards, All-Star selections | Basketball-Reference, Wikipedia; HTML parsing, web scraping and reshaping |
| S15–S16 | Front office and ownership history | Basketball-Reference, RealGM |
| S17 | NBA draft history | Basketball-Reference |
| S18 | Luxury-tax history | Spotrac and historical tax records |

**Coverage notes:**
- Historical coverage differs across datasets.
- Some financial fields are unavailable or not applicable in earlier seasons.
- S11C and S12 are deferred, as discussed in Section 5.

## 3. Data Acquisition & Staging Methodology

For each group, document:
- **Source and extraction:** Where the data originated and how it was obtained.
- **Data grain:** What one row represents.
- **Key transformations:** How the raw data became a staged table.
- **Validation / limitations:** Important checks, inconsistencies, and known gaps.

### Group 1 — Team Performance & Playoffs (S01–S03)

### Group 1 — Team Performance & Playoffs (S01–S03)

**Source and extraction:** Historical NBA standings, team-level advanced statistics, and playoff results were collected from Basketball-Reference using `pandas.read_html()`. Season-level datasets were standardized and consolidated into staged source tables.

**Data grain:**
- **S01 — Standings:** One row per franchise-season.
- **S02 — Team advanced statistics:** One row per franchise-season.
- **S03 — Playoffs:** Original data contained one row per playoff series; the staged team-season postseason output was derived at one row per franchise-season.

**Key transformations:**
- Standardized historical team names, season identifiers, and source-table structures.
- Cleaned and consolidated season-level standings and advanced-statistics records.
- Restructured playoff series into team-level observations, mapped postseason rounds, and aggregated each team's deepest playoff progression to the franchise-season grain.

**Validation and limitations:**
- Franchise-season uniqueness and historical season coverage are important validation requirements.
- Playoff progression must be consistent with series results and historical postseason formats.
- Historical franchise naming conventions and changes in source-table structures require normalization.

*Validation requirements are described here; individual checks should be verified against the notebooks before being represented as completed.*

### Group 2 — Rosters & Player Statistics (S04–S06)

### Group 2 — Rosters & Player Statistics (S04–S06)

**Source and extraction:** Historical NBA roster information, player per-game statistics, and advanced player statistics were retrieved using the NBA API Python wrapper. Data was collected season by season through API requests, accumulated as individual DataFrames in a Python list, and consolidated using `pd.concat()`.

**Data grain:**
- **S04 — Rosters:** Player-team-season records.
- **S05 — Player per-game statistics:** Player-season or player-team-season records, depending on source representation.
- **S06 — Player advanced statistics:** Player-season or player-team-season records, depending on source representation.

**Key transformations:**
- Iterated through historical seasons to retrieve and consolidate API responses into consistent staged datasets.
- Standardized season identifiers, relevant player/team fields, column structures, and data types.
- Used list accumulation followed by concatenation to avoid repeated DataFrame expansion during ingestion.
- Accounted for differences between player-season and player-team-season observations, particularly for players associated with multiple franchises.

**Validation and limitations:**
- API availability, request failures, and historical coverage can affect completeness.
- Schema consistency and season coverage require verification across retrieved responses.
- Players appearing for multiple teams in a season require careful treatment to avoid duplicate counting in downstream analysis.

### Group 3 — Player Salaries (S07)

### Group 3 — Player Salaries (S07)

**Source and extraction:** Historical NBA player salary records were collected from ESPN's paginated salary tables. Because each season's records were distributed across multiple webpages, extraction required nested iteration over seasons and their corresponding pages.

**Data grain:** Player-season salary records, subject to verification of source-specific player/team representation.

**Key transformations:**
- Used an outer loop to iterate through historical seasons and an inner loop to retrieve multiple salary pages within each season.
- Collected individual page-level DataFrames and consolidated them into a standardized historical salary dataset.
- Standardized column names, season identifiers, and salary fields, including converting currency-formatted salary values into numeric data.
- Used page-completion logic to determine when the available records for each season had been retrieved.

**Validation and limitations:**
- Pagination completeness is important because missing pages can result in incomplete season-level salary records.
- Request failures and unexpected page structures must be distinguished from legitimate end-of-pagination conditions.
- Player identity, season coverage, and salary definitions require careful reconciliation with other sources during canonical modeling.

### Group 4 — NBA Financial Data (S08–S11)

### Group 4 — NBA Financial Data (S08–S11)

**Source and extraction:** Historical NBA team payroll, salary-cap thresholds, and related financial reference data were collected primarily from Spotrac, RealGM, and other historical sources. Automated HTML extraction was attempted but encountered HTTP 403 access restrictions on certain Spotrac pages. Manual table collection into CSV files was used as a practical alternative.

**Data grain:** Primarily franchise-season records for team financial data and season-level records for league-wide financial thresholds, depending on the source table.

**Key transformations:**
- Manually collected historical financial tables and added explicit season/year identifiers to preserve the reporting period of each dataset.
- Standardized column names and structures across annual source files, removing repeated headers, separator rows, and formatting artifacts.
- Cleaned financial fields by removing currency symbols, separators, and whitespace, then converting relevant string columns into numeric data types.
- Used filtering and data-type checks to distinguish valid observations from non-data rows before consolidating historical records.

**Validation and limitations:**
- Manual collection introduces potential transcription, missing-row, and season-labeling errors, requiring source reconciliation and completeness checks.
- Financial definitions, available fields, and reporting conventions may differ across seasons and sources.
- Missing or historically inapplicable financial thresholds should remain distinguishable from actual zero values.

### Group 5 — CBA History & Regime Mapping (S13)

### Group 5 — CBA History & Regime Mapping (S13)

**Source and extraction:** Historical NBA collective bargaining agreements were identified using reference materials including Larry Coon's CBA FAQ and RealGM. Rather than extracting an existing standardized dataset, CBA agreement history and season-level regime mappings were constructed from historical reference information.

**Data grain:**
- **CBA agreement history:** One row per collective bargaining agreement.
- **Season-to-CBA mapping:** One row per NBA season, identifying the governing agreement.

**Key transformations:**
- Identified five historical CBA regimes relevant to the project period: 1998, 2005, 2011, 2017, and 2023.
- Constructed a season-level mapping to associate each NBA season with its governing CBA regime.
- Preserved the distinction between agreement-level and season-level records, allowing one agreement to relate to multiple seasons.
- Organized the mapping to support future joins with player-season, franchise-season, and other season-based datasets.

**Validation and limitations:**
- Each season should map to exactly one governing CBA agreement.
- Agreement effective dates and season assignments require verification against historical reference materials.
- Individual CBA provisions may take effect at different times, so the governing agreement alone does not establish when every rule became operational.

### Group 6 — Awards & Player Recognition (S14A–S14C)

### Group 6 — Awards & Player Recognition (S14A–S14C)

**Source and extraction:** Historical All-NBA selections, individual awards, and All-Star records were collected from Basketball-Reference and Wikipedia. Extraction methods included `pandas.read_html()` and HTML parsing with BeautifulSoup, including access to Basketball-Reference tables embedded within HTML comments.

**Data grain:**
- **S14A — All-NBA selections:** One player-season-All-NBA team selection.
- **S14B — Individual awards:** Player-season-award records.
- **S14C — All-Star selections:** Player-season selection records.

**Key transformations:**
- **S14A:** Reshaped All-NBA tables from a wide structure containing five player slots per team into a long structure with one row per player selection using `pd.melt()`.
- **S14B:** Parsed HTML to retrieve award tables, including tables stored within HTML comments, then standardized award names, player information, and seasons.
- **S14C:** Normalized historical Wikipedia All-Star tables with varying column structures and formatting conventions.
- Consolidated historical records into consistent staged datasets suitable for player-level analysis.

**Validation and limitations:**
- All-NBA selection counts can be checked against the expected number of selections per season, accounting for historical exceptions.
- Player names, award categories, and season identifiers require consistent standardization across sources.
- Historical changes in award eligibility, selection formats, and webpage structures may affect comparability.

### Group 7 — Organizational History & Draft (S15–S17)

### Group 7 — Organizational History & Draft (S15–S17)

**Source and extraction:** Historical NBA general manager/front-office tenures, ownership records, and draft selections were collected from Basketball-Reference and RealGM. Source data was extracted from historical franchise and draft tables and standardized for the project period.

**Data grain:**
- **S15 — Front office history:** One record per executive-franchise tenure.
- **S16 — Ownership history:** One record per owner-franchise tenure.
- **S17 — Draft history:** One record per draft selection within a draft year.

**Key transformations:**
- **S15–S16:** Standardized franchise identifiers and converted tenure start/end dates into consistent date representations.
- Retained tenures overlapping the historical analysis period, including those beginning before the project window and those still ongoing.
- Preserved tenure-level observations rather than prematurely assigning executives or owners to individual seasons, allowing later modeling of leadership changes within seasons.
- **S17:** Consolidated annual draft tables, standardized draft years and franchise information, and identified draft rounds while accounting for historical changes in draft structure.

**Validation and limitations:**
- Executive and ownership records require careful handling of ongoing tenures, incomplete dates, and multiple distinct tenures.
- Assigning a decision-maker to a particular NBA season will require an explicit temporal matching rule during canonical modeling.
- Draft records require checks for unique selections, historical team counts, and source-format differences.

### Group 8 — Luxury-Tax History (S18)

### Group 8 — Luxury-Tax History (S18)

**Source and extraction:** Historical NBA luxury-tax data was assembled from modern Spotrac records and older historical tax tables. Modern data was collected from season-specific financial tables, while older records required additional restructuring to align with the modern dataset.

**Data grain:** One franchise-season record.

**Key transformations:**
- Reshaped historical luxury-tax records from a wide structure (one franchise per row, multiple season columns) into a long structure using `pd.melt()`, producing one observation per franchise-season.
- Standardized season identifiers, franchise names, financial values, and column structures across historical and modern datasets.
- Aligned the available financial fields and concatenated the datasets into a consolidated historical luxury-tax table.
- Preserved franchise-season records with missing tax values rather than removing teams that did not report a tax payment.

**Validation and limitations:**
- Historical and modern sources contain different levels of financial detail, requiring explicit handling of unavailable fields.
- Missing tax values must be distinguished from confirmed zero-tax payments or non-taxpayer status based on source definitions.
- Taxable payroll, tax thresholds, estimated liabilities, and actual tax payments are distinct financial measures and should not be treated interchangeably.
- Retaining both taxpaying and non-taxpaying franchises is necessary for comparing ownership spending behavior, financial constraints, and competitive outcomes.

## 4. Data Quality & Validation

Data acquisition and staging required standardization across multiple historical sources, extraction methods, and reporting formats.

The following quality dimensions guide validation of the staged datasets:

- **Data grain and uniqueness:** Verify that records conform to their intended observation level, such as player-season, franchise-season, or individual transaction/selection.
- **Historical coverage:** Check season ranges, expected franchise counts, and completeness across the 2000–01 through 2025–26 analysis period.
- **Schema consistency:** Standardize column names, data types, season identifiers, and historical franchise naming conventions.
- **Missing values:** Distinguish genuinely unavailable information from confirmed zero values, nonapplicable fields, and extraction failures.
- **Source reconciliation:** Compare important record counts, financial figures, and historical outcomes against original sources where practical.
- **Transformation integrity:** Confirm that reshaping, aggregation, and concatenation preserve the intended observations without introducing unintended duplicates or omissions.

The staged source datasets underwent validation prior to completion of the initial acquisition and staging phase. Checks included expected row and column counts, intended data grain, column data types, historical coverage, missing-value handling, and reconciliation against the corresponding raw source data.

These checks supported the completion of source-level staging for the initial project scope, excluding the explicitly deferred S11C and S12 datasets.

Additional validation will be performed during canonical modeling to verify cross-source identifier consistency, join cardinality, relational integrity, and preservation of records across integrated datasets.

## 5. Deferred Sources & Known Limitations


Two planned source datasets were deferred after evaluating historical coverage, accessibility, engineering complexity, and incremental analytical value.

### S11C — Full Player Contract History

Comprehensive contract-level records would enable analysis of signing dates, extensions, contract duration, negotiated terms, and the timing of financial commitments by NBA franchises.

However, detailed historical contract information was substantially restricted by paid access requirements, incomplete historical coverage, and inconsistent player-level availability. The project prioritizes freely accessible data and avoids substantial acquisition costs.

Existing player salary, team payroll, and CBA datasets support season-level financial analysis, but they do not fully reconstruct the contractual decisions underlying those expenditures. Selected historical contracts may be examined separately as case studies where reliable information is available.

### S12 — Player Transactions

Historical transaction records would support detailed analysis of trades, signings, waivers, extensions, and roster-management decisions.

Available transaction histories presented substantial pagination and semi-structured textual data, requiring extensive extraction, parsing, entity standardization, and event classification. Historical coverage was also incomplete relative to the project's full analysis period.

Rather than building a comprehensive transaction event database in the initial version, the project will prioritize franchise-season financial and competitive analysis. Selected transactions may be incorporated as contextual case studies.

### Scope and Analytical Implications

These sources were deferred based on the expected engineering effort relative to their incremental value for the initial research objectives—not because contract and transaction histories lack analytical importance.

The current dataset supports investigation of franchise spending behavior, competitive outcomes, and CBA-related financial constraints. However, causal claims about individual contract decisions, transaction strategies, or management intent will require additional evidence.

Comprehensive contract and transaction datasets remain potential future extensions.

## 6. Next Stage — Canonical Data Modeling

Following source acquisition and staging, the next phase will consolidate the historical datasets into a smaller set of canonical relational tables.

The objective is to establish consistent franchise, player, and season identifiers; define table grains and primary keys; resolve cross-source inconsistencies; and connect team performance, roster composition, financial data, CBA regimes, and organizational history.

Canonical modeling will distinguish source-level observations from standardized analytical entities, with explicit rules for joins, historical franchise changes, player-team relationships, and temporal records.

The resulting tables will provide a reproducible foundation for SQL analysis, exploratory data analysis, and investigations into NBA spending behavior, roster construction, competitive outcomes, and the financial incentives created by successive collective bargaining agreements.

Canonical table definitions, transformation logic, and validation results will be documented as this phase is implemented.