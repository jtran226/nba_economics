# NBA Economics: Financial Incentives, Roster Construction & Competitive Outcomes

## Project Overview

This project investigates how NBA franchise owners and general managers allocate financial resources, construct rosters, and make competitive decisions under evolving collective bargaining agreements (CBAs).

Using historical data from the 2000–01 through 2025–26 NBA seasons, the project examines relationships between franchise spending, player compensation, organizational decision-making, and competitive performance.

A particular focus is the NBA's 2023 CBA and its first- and second-apron restrictions, which introduce additional financial and operational constraints on high-spending teams.

The broader objective is to understand how economic incentives influence franchise behavior, roster construction, and championship contention across different regulatory environments.

## Research Objectives

The project is organized around six primary research areas:

1. **Spending and winning:** How strongly does franchise spending correlate with regular-season performance and playoff success?
2. **Financial efficiency:** Which franchises consistently achieve stronger competitive outcomes relative to their payroll commitments?
3. **Ownership and management behavior:** How do spending patterns and competitive strategies differ across ownership groups and front-office regimes?
4. **CBA incentives:** How have salary-cap rules, luxury-tax thresholds, and apron restrictions influenced franchise spending and roster-construction decisions?
5. **Roster construction:** What financial and roster characteristics distinguish championship contenders from less competitive franchises?
6. **Star concentration and roster balance:** How does the allocation of payroll between star players and supporting contributors affect team performance, and which roster structures appear most effective?

These questions represent initial research directions and may evolve as exploratory analysis, feature engineering, and statistical modeling progress.

## Data Sources and Coverage

The project assembles historical data from sources including:

- Basketball-Reference
- NBA API
- ESPN
- Spotrac
- RealGM
- Larry Coon's NBA CBA FAQ
- Wikipedia

The datasets cover team performance, playoff outcomes, player statistics, rosters, player salaries, franchise payroll, luxury-tax history, salary-cap rules, rookie and minimum salary schedules, awards, and organizational leadership.

The canonical dataset spans **26 NBA seasons (2000–01 through 2025–26)**.

Historical coverage and completeness vary by source and variable. Known limitations, unresolved records, and historical inconsistencies are documented in the Source Methodology.

## Data Pipeline

The project follows a four-stage workflow:

**Raw data → Staging datasets → Canonical relational tables → Analytical features and research outputs**

- **Raw:** Original downloaded, extracted, or manually collected source records.
- **Staging:** Cleaned and standardized source-specific datasets, preserving relevant source definitions.
- **Canonical:** Integrated datasets with consistent player, franchise, and season identifiers, defined analytical grains, and validated relationships.
- **Analysis:** Research-specific feature engineering, aggregation, exploratory analysis, statistical modeling, and visualizations.

### Canonical Data Model

Phase A produced nine logical canonical tables (C01–C09):

| Table | Description |
|---|---|
| C01 — Seasons | Salary caps, tax thresholds, apron limits, and CBA regimes |
| C02 — Franchises | Stable franchise identities and historical team-name aliases |
| C03 — Players | Canonical player identities and biographical attributes |
| C04 — Team Seasons | Team performance, playoff outcomes, payroll, and luxury-tax data |
| C05 — Player-Team Seasons | Player roster membership and performance statistics |
| C06 — Leadership Tenures | Ownership and front-office leadership history |
| C07 — Player Salaries | Annual player compensation by franchise and season |
| C08 — Player Awards | Individual awards, selections, and voting records |
| C09 — Salary Rules | Rookie salary scales and minimum salary schedules |

Supporting datasets preserve historical franchise aliases and unresolved player-salary observations.

The canonical model is designed to support joins across multiple analytical grains while preserving source-specific limitations.

## Tools and Technologies

- **Python:** Data extraction, cleaning, transformation, analysis, and statistical modeling
- **pandas / NumPy:** DataFrame operations, data transformation, and numerical analysis
- **BeautifulSoup:** HTML parsing where required
- **NBA API:** Programmatic retrieval of NBA data
- **SQL:** Relational modeling and analytical queries
- **Jupyter Notebooks:** Data engineering, exploratory analysis, and documentation
- **Git / GitHub:** Version control and project publication

## Project Status

### Phase A — Data Acquisition, Staging & Canonical Integration: Complete

- Historical source datasets acquired and staged for the initial research scope
- Raw-to-staging transformations and source-level validation completed
- Nine logical canonical tables constructed for 2000–01 through 2025–26
- Player, franchise, and season identifiers standardized across integrated datasets
- Canonical relationships validated for key uniqueness, foreign-key integrity, and franchise-season consistency
- Source methodology, historical limitations, and unresolved salary observations documented

The consolidated structural audit passed all executed row-count, primary-key, foreign-key, and franchise-season relationship checks.

Player-salary matching currently covers **82.76% of canonical roster observations**. Historical luxury-tax and other source-specific coverage limitations remain documented rather than artificially resolved.

### Phase B — Analytical Feature Engineering & Exploratory Analysis: Next

The next phase will construct research-specific analytical datasets and derived features, including:

- Franchise spending relative to salary-cap and luxury-tax thresholds
- Payroll efficiency and competitive outcomes
- Player compensation relative to individual production
- Salary concentration and roster balance
- Franchise performance across CBA regimes
- Ownership and front-office tenure relationships

Features will be defined at appropriate analytical grains, with explicit treatment of aggregation, missingness, historical comparability, and sample selection.

### Phase C — Statistical Modeling & Research Outputs: Planned

- Exploratory and comparative statistical analysis
- Franchise spending and competitive-efficiency comparisons
- Historical CBA and luxury-tax behavior analysis
- Roster-construction and player-value case studies
- Classification or predictive modeling where appropriate
- Research write-ups and visualizations

## Documentation

See [Source Methodology](docs/source_methodology.md) for source acquisition, raw-to-staging transformations, canonical integration, data-model rationale, validation results, and known limitations.

## Planned Outputs

- Validated canonical datasets — **completed**
- Research-specific analytical feature tables
- Reproducible SQL and Python analysis
- Franchise spending and competitive-efficiency comparisons
- Historical CBA and luxury-tax analysis
- Selected roster-construction and compensation case studies
- Public research write-ups and visualizations

This repository is an ongoing analytical research project. Analytical findings and interpretations will be added as Phase B and Phase C progress.