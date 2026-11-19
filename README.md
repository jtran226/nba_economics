# NBA Economics: Financial Incentives, Roster Construction & Competitive Outcomes

## Project overview

This project investigates how NBA franchise owners and general managers allocate financial resources, construct rosters, and make competitive decisions under evolving collective bargaining agreements (CBAs).

Using historical data from the **2000–01 through 2025–26 NBA seasons**, it examines relationships among franchise spending, player compensation, organizational decision-making, and competitive performance. A particular focus is the **2023 CBA**, including its first- and second-apron restrictions and their implications for high-spending teams.

The broader goal is to understand how salary-cap rules, luxury-tax incentives, and roster-building constraints relate to franchise behavior, competitive efficiency, and championship contention across different regulatory environments.

## Research objectives

The project is organized around six research areas:

1. **Spending and winning:** How strongly does franchise spending correlate with regular-season performance and playoff success?
2. **Financial efficiency:** Which franchises achieve stronger competitive outcomes relative to their payroll commitments?
3. **Ownership and management behavior:** How do spending patterns and competitive strategies differ across ownership groups and front-office regimes?
4. **CBA incentives:** How do salary-cap rules, luxury-tax thresholds, and apron restrictions relate to franchise spending and roster-construction decisions?
5. **Roster construction:** What financial and roster characteristics distinguish championship contenders from less competitive franchises?
6. **Star concentration and roster balance:** How does payroll allocation between star players and supporting contributors relate to team performance?

These are research directions, not established causal findings. Specific hypotheses, eligible samples, and methods will be defined for each investigation.

## Data sources and coverage

Historical records were collected from **Basketball-Reference, NBA API, ESPN, Spotrac, RealGM, Larry Coon's NBA CBA FAQ, and Wikipedia**, using programmatic extraction and manual collection where necessary.

The integrated model covers team performance, playoffs, rosters, player statistics, salaries, franchise payroll, luxury-tax history, salary-cap and apron rules, rookie and minimum salary schedules, awards, and organizational leadership. Coverage varies by source, variable, and regulatory period. Missing or unresolved observations are preserved and documented rather than automatically treated as zero.

## Data pipeline

**Raw source records → Staged source tables → Canonical relational layer → Reusable analytical datasets → Research-specific analyses**

- **Raw:** Downloaded, extracted, or manually collected source records.
- **Staging:** Cleaned and standardized source-specific tables, retaining relevant source definitions.
- **Canonical (Phase A):** Shared player, franchise, and season identifiers; explicit grains; tested relational integrity.
- **Analytical (Phase B):** Reusable franchise- and player-level features, persisted as Parquet datasets.
- **Research (next):** Question-specific sample selection, exploratory analysis, statistical models, and public findings.

### Canonical data model

Phase A established ten logical canonical groups (C01–C10); C09 contains two distinct salary-rule tables.

| Group | Dataset | Purpose |
|---|---|---|
| C01 | `seasons.csv` | Salary caps, tax thresholds, apron reference values, and CBA regimes |
| C02 | `franchises.csv` | Stable franchise identities and historical aliases |
| C03 | `players.csv` | Canonical player identities and biographical/draft attributes |
| C04 | `team_seasons.csv` | Team results, playoffs, payroll, and luxury-tax measures |
| C05 | `player_team_seasons.csv` | Player–franchise–season performance and roster observations |
| C06 | `leadership_tenures.csv` | Ownership and front-office leadership history |
| C07 | `player_salaries.csv` | Annual player compensation by franchise and season |
| C08 | `player_awards.csv` | Awards, selections, and voting observations |
| C09a | `rookie_salary_scale.csv` | Rookie salary-scale rules |
| C09b | `minimum_salary_rules.csv` | Minimum salary schedules by experience |
| C10 | `team_apron_seasons.csv` | Apron allocations, thresholds, positions, and source-supported hard-cap evidence |

C10 contains **450 franchise-seasons** (2011–12 through 2025–26); second-apron observations apply from 2023–24 onward. Supporting canonical files retain franchise-season aliases and unresolved player-salary records. Different financial measures remain separate when their accounting definitions or reporting dates may differ.

## Analytical layer — Phase B

Both analytical datasets have been constructed and documented. They serve different observation grains and are intended as reusable foundations rather than one-off, research-question-specific extracts.

| Dataset | Grain | Rows | Persisted columns | Feature scope |
|---|---|---:|---:|---|
| **A01 — Franchise-season** | `(franchise_id, season)` | 776 | 210 | 73 inherited/joined; 137 engineered |
| **A02 — Player–franchise–season** | `(player_id, franchise_id, season)` | 12,810 | 183 | 62 base/inherited; 121 F1–F5 analytical fields |

**A01** combines C04 team seasons, C01 league rules, and C10 apron data. Its engineered features cover financial normalization and exposure, league-relative spending and outcomes, efficiency, year-over-year change, multi-season windows, and state transitions.

**A02** builds on C05 player performance and joins player identity, matched salaries, league financial rules, awards, and selected A01 franchise context. Its F1–F5 stages cover compensation and coverage, recorded production shares, compensation-versus-production comparisons, recognition, and historical player observations. The 121 analytical fields include joined context fields, not exclusively new calculations.

A01 and A02 are **not interchangeable grains**. Player-season awards may repeat across a traded player's franchise rows; franchise totals must not be summed after joining them onto individual-player records without a deliberate aggregation policy.

### Analytical artifacts

| Artifact | A01 | A02 |
|---|---|---|
| Build and validation notebook | [`notebooks/a01_franchise_season_build.ipynb`](notebooks/a01_franchise_season_build.ipynb) | [`notebooks/a02_player_franchise_season.ipynb`](notebooks/a02_player_franchise_season.ipynb) |
| Persisted output | `data/analytical/a01_franchise_season.parquet` | `data/analytical/a02_player_franchise_season.parquet` |
| Feature inventory (CSV) | [`docs/a01_feature_library.csv`](docs/a01_feature_library.csv) | [`docs/a02_feature_library.csv`](docs/a02_feature_library.csv) |
| Feature definitions and design notes | [`docs/a01_feature_library.md`](docs/a01_feature_library.md) | [`docs/a02_feature_library.md`](docs/a02_feature_library.md) |

## Validation and interpretive limits

- **Canonical integrity:** Phase A's executed structural checks passed for tested row counts, primary/foreign keys, and franchise-season relationships. This does not independently validate every historical source definition.
- **A01:** The documented export contains 776 rows and 210 columns with recorded grain, coverage, and export checks. Historical threshold comparisons may be descriptive accounting proxies rather than verified official regulatory status.
- **A02:** The documented output contains 12,810 rows and 183 columns. **10,601** C05 observations have exact salary matches; **2,209** do not (**82.76%** matched). Separately, **380** source salary observations remain unresolved. These are different populations, and missing salary does not mean zero salary.
- **Financial accounting:** Headline payroll, salary-cap hit, taxable payroll, apron allocations, actual tax paid, and estimated liabilities are not automatically interchangeable. Official tax/apron classifications require appropriate regulatory numerators, definitions, and measurement dates.
- **Player attribution:** C05 does not fully reconstruct every traded-player stint; A02's salary and production comparisons are descriptive, not estimates of causal player value.
- **Historical comparability:** Season lengths, CBA rules, award structures, source coverage, and applicable apron regimes vary over time.

The feature libraries and [source methodology](docs/source_methodology.md) document field-level coverage, construction decisions, and remaining reconciliation needs. Completion of documentation does **not** imply a fresh-kernel rebuild was executed during the documentation update.

## Reproducibility and tools

The workflow uses **Python, pandas, NumPy, BeautifulSoup, NBA API, SQL/DuckDB, Jupyter, Parquet, Git, and GitHub**. Acquisition and staging include automated and manual workflows depending on source accessibility.

To reproduce the analytical layer, prepare the canonical inputs in their expected repository locations, run the **A01 notebook before A02** (A02 consumes selected A01 fields), and validate the final keys, schema, coverage, and exported values against the saved Parquet outputs. Notebook temporary views are session-scoped; run construction cells in order. Consult the notebooks for exact executable transformations rather than treating the README as a complete environment/setup specification.

## Project status and next steps

- **Phase A — Acquisition, staging, canonical integration:** Completed for the defined baseline, with documented exceptions and source limitations.
- **Phase B — Feasibility and reusable analytical construction:** A01 and A02 datasets, feature inventories, and methodological documentation completed. Final repository-level verification and freeze remain to be confirmed.
- **Phase C — Statistical analysis and research outputs:** Next. Start with descriptive EDA and spending/competitive comparisons, then move to explanatory models, predictive analyses, and causal-inference-oriented designs where the data support them.

Full contract history (S11C) and transaction event history (S12) remain deferred. Leadership attribution and regulatory-accounting reconciliation should be addressed when a research question materially depends on them, rather than represented as completed features.

## Documentation

- **[Source methodology](docs/source_methodology.md):** Raw acquisition, staging, canonical integration, Phase B construction, validation, and limitations.
- **[A01 feature library](docs/a01_feature_library.md)** and **[A01 inventory CSV](docs/a01_feature_library.csv):** Franchise-season feature definitions and coverage.
- **[A02 feature library](docs/a02_feature_library.md)** and **[A02 inventory CSV](docs/a02_feature_library.csv):** Player–franchise–season feature definitions and coverage.

This repository is an ongoing research project. Analytical findings, visualizations, and public write-ups will be added as investigations are completed.
