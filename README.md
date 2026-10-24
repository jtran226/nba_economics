# NBA Economics: Financial Incentives, Roster Construction & Competitive Outcomes

## Project Overview

This project investigates how owners and GMs of NBA franchises allocate financial resources, construct rosters, and compete for championships (or lack thereof to place bets on competing in future NBA seasons) under evolving collective bargaining agreements (CBAs).

Using historical data from the 2000–01 through 2025–26 NBA seasons, the project examines relationships between franchise spending, player compensation, organizational decision-making, and competitive performance.

A particular focus is the NBA's 2023 CBA and its first- and second-apron restrictions, which introduce additional constraints on roster construction for high-spending teams.

## Research Objectives

The project is organized around several analytical questions:

1. **Spending and winning:** How strongly does franchise spending correlate with regular-season and playoff success?
2. **Financial efficiency:** Which franchises consistently generate stronger competitive outcomes relative to their payroll commitments?
3. **Ownership and management behavior:** How do spending patterns differ across franchises, owners, and front-office regimes?
4. **CBA incentives:** How have salary-cap rules, luxury-tax thresholds, and apron restrictions influenced team-building strategies?
5. **Roster construction:** What financial and roster characteristics distinguish successful contenders from less competitive teams?
6. ** What is the ideal makeup of a roster based on star-power and filling out the rest of the roster with quality players and how do the contracts represent that value?

These questions represent initial research directions and may evolve as the canonical data model and analysis develop.

## Data Sources and Coverage

The project assembles historical data from sources including but not limited to:

- Basketball-Reference
- NBA API
- ESPN
- Spotrac
- RealGM
- Larry Coon's NBA CBA FAQ
- Wikipedia

The source datasets cover team performance, playoff outcomes, player statistics, rosters, salaries, league financial rules, luxury-tax history, player recognition, draft history, and organizational leadership.

Historical coverage and completeness vary by dataset.

## Data Pipeline

The project follows a staged analytical workflow:

**Raw data → Staged datasets → Canonical relational tables → Analysis and research outputs**

- **Raw:** Original downloaded, extracted, or manually collected source records.
- **Staging:** Cleaned and standardized source-specific datasets.
- **Canonical:** Planned relational tables with consistent identifiers, defined grains, and documented relationships.
- **Analysis:** SQL, Python, statistical analysis, and visualizations addressing the project's research questions.

## Tools and Technologies

- **Python:** Data extraction, cleaning, transformation, analysis, statistical modeling, early-stage machine learning and classification
- **pandas / NumPy:** DataFrame operations and numerical processing
- **BeautifulSoup:** HTML parsing where required
- **NBA API:** Programmatic retrieval of NBA data
- **SQL:** Relational modeling and analytical queries
- **Jupyter Notebooks:** Development, exploration, and documentation
- **Git / GitHub:** Version control and project publication

## Project Status

**Phase 1 — Source Acquisition, Staging & Validation: Complete**

- Historical source datasets acquired and staged for the initial project scope
- Source-level transformations and validation checks completed
- Source methodology and known limitations documented
- S11C (full contract history) and S12 (transactions) explicitly deferred

**Phase 2 — Canonical Data Modeling: Next**

- Define canonical table grains, primary keys, and relationships
- Standardize franchise, player, and season identifiers across sources
- Integrate staged datasets into canonical relational tables
- Validate joins, uniqueness, referential integrity, and historical consistency

**Phase 3 — Analysis & Research: Planned**

- Franchise spending and competitive outcomes
- Financial efficiency and luxury-tax behavior
- Ownership and front-office decision-making
- CBA regime comparisons and roster-construction strategies

## Documentation

See [Source Methodology](docs/source_methodology.md) for source-level extraction methods, data grains, transformations, limitations, and validation requirements.

## Planned Outputs

- Canonical relational datasets
- Reproducible SQL and Python analysis
- Franchise spending and competitive-efficiency comparisons
- Historical CBA and luxury-tax analysis
- Selected roster-construction and contract case studies
- Research write-ups and visualizations

This repository is an ongoing analytical project. Findings and interpretations will be added as the data model and analyses are completed.