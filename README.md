NBA Economics: Financial Incentives, Roster Construction & Competitive Outcomes
Project Overview
This project investigates how NBA franchise owners and general managers allocate financial resources, construct rosters, and make competitive decisions under evolving collective bargaining agreements (CBAs).

Using historical data from the 2000–01 through 2025–26 NBA seasons, it examines relationships among franchise spending, player compensation, organizational decision-making, and competitive performance. A particular focus is the 2023 CBA, including its first- and second-apron restrictions and their implications for high-spending teams.

The broader goal is to understand how salary-cap rules, luxury-tax incentives, and roster-building constraints relate to franchise behavior, competitive efficiency, and championship contention across different regulatory environments.
Research Objectives
The project is organized around six research areas:
1. Spending and winning: How strongly does franchise spending correlate with regular-season performance and playoff success?
2. Financial efficiency: Which franchises achieve stronger competitive outcomes relative to their payroll commitments?
3. Ownership and management behavior: How do spending patterns and competitive strategies differ across ownership groups and front-office regimes?
4. CBA incentives: How do salary-cap rules, luxury-tax thresholds, and apron restrictions relate to franchise spending and roster-construction decisions?
5. Roster construction: What financial and roster characteristics distinguish championship contenders from less competitive franchises?
6. Star concentration and roster balance: How does payroll allocation between star players and supporting contributors relate to team performance?
These are initial research directions. Specific hypotheses, analytical samples, and methods will be refined during feature engineering and exploratory analysis.
Data Sources and Coverage
The project draws on historical records from Basketball-Reference, NBA API, ESPN, Spotrac, RealGM, Larry Coon's NBA CBA FAQ, and Wikipedia.

The integrated data cover team performance, playoff outcomes, player statistics and rosters, player salaries, franchise payroll, luxury-tax history, salary-cap and apron rules, rookie and minimum salary schedules, awards, and organizational leadership.

The canonical model spans 26 NBA seasons (2000–01 through 2025–26), although coverage varies by source, variable, and regulatory period. Missing or unresolved observations are retained and documented rather than automatically treated as zero.
Data Pipeline
Raw source data → Staging datasets → Canonical relational tables → Analytical features and research outputs
- Raw: Original downloaded, extracted, or manually collected records.
- Staging: Cleaned and standardized source-specific datasets that preserve relevant source definitions.
- Canonical: Integrated datasets with consistent player, franchise, and season identifiers, defined observation grains, and validated relationships.
- Analysis: Research-specific features, aggregations, exploratory analysis, statistical models, and visualizations.
Canonical Data Model
Phase A established ten logical canonical groups (C01–C10). C09 consists of two separate salary-rule tables.
Group	Dataset	Description
C01	seasons.csv	Season-level salary caps, tax thresholds, apron reference values, and CBA regimes
C02	franchises.csv	Stable franchise identities, supported by historical team-name aliases
C03	players.csv	Canonical player identities and biographical attributes
C04	team_seasons.csv	Team performance, playoff outcomes, payroll, and luxury-tax measures
C05	player_team_seasons.csv	Player roster membership and performance statistics
C06	leadership_tenures.csv	Ownership and front-office leadership history
C07	player_salaries.csv	Annual player compensation by franchise and season
C08	player_awards.csv	Individual awards, selections, and voting observations
C09a	rookie_salary_scale.csv	Rookie salary-scale rules
C09b	minimum_salary_rules.csv	Minimum salary schedules by experience
C10	team_apron_seasons.csv	Team apron allocations, first- and second-apron thresholds, spending positions, and source-supported hard-cap classifications


C10 covers 450 franchise-seasons (2011–12 through 2025–26). Second-apron observations apply from 2023–24 onward. Supporting files preserve franchise-season aliases and unresolved player-salary records.

The canonical layer separates measures that may have different accounting definitions or reporting dates rather than forcing them into a single field.
Tools and Technologies
- Python, pandas, and NumPy: Extraction, cleaning, transformation, numerical analysis, and feature engineering.
- BeautifulSoup and NBA API: HTML parsing and programmatic data acquisition.
- SQL: Relational modeling and analytical queries.
- Jupyter Notebooks: Reproducible data engineering, validation, and exploratory analysis.
- Git and GitHub: Version control and project publication.
Project Status
Phase A — Data Acquisition, Staging & Canonical Integration: Complete
- Acquired and staged the initial historical source datasets, including S19 apron allocations.
- Constructed and integrated the C01–C10 canonical groups, with consistent franchise, player, and season identifiers.
- Validated the tested row counts, primary and foreign keys, and franchise-season relationships.
- Documented source methodology, financial-measure discrepancies, unresolved player-salary observations, and historical coverage limitations.
The original canonical audit passed its executed structural checks. Subsequent S19/C10 checks confirmed 450 unique franchise-season records, all matching C04 keys. These results establish structural consistency for the tested relationships, not independent verification of every historical accounting definition.

Exact salary matching covers 82.76% of canonical roster observations. Salary-dependent analyses will account for unmatched and unresolved records.
Phase B — Feature Feasibility Complete; Analytical Engineering Next
A feasibility audit evaluated six feature families: financial thresholds, player compensation, roster composition, leadership, CBA regimes, and competitive outcomes. All six were found feasible within their defined scopes, with documented limitations involving source coverage, roster attribution, and historical comparability.

Planned feature development includes:
- Franchise spending relative to salary-cap, luxury-tax, and apron thresholds.
- Payroll efficiency and competitive outcomes.
- Player compensation relative to individual production.
- Salary concentration, roster balance, and retention.
- Franchise performance across CBA regimes.
- Ownership and front-office tenure relationships.
Important limitation: Headline payroll, salary-cap accounting, Tax Team Salary, and Apron Team Salary are not interchangeable. Before producing official threshold-sensitive classifications, the project must reconcile the relevant source definitions, regulatory salary numerators, and measurement dates. Unverified threshold comparisons will be labeled as descriptive proxies.

Current status (November 2026): S19/C10 integration and the canonical coverage audits are complete. The next milestone is to construct research-specific analytical tables and features while addressing the financial-accounting reconciliation required for official cap, tax, and apron exposure measures.
Phase C — Statistical Modeling & Research Outputs: Planned
- Exploratory and comparative statistical analysis.
- Franchise spending and competitive-efficiency comparisons.
- Historical CBA and luxury-tax behavior analysis.
- Roster-construction and player-value case studies.
- Classification or predictive modeling where justified.
- Public research write-ups and visualizations.
Documentation
See [Source Methodology](docs/source_methodology.md) for acquisition methods, raw-to-staging transformations, canonical integration, validation findings, source reconciliation, and known limitations.
Planned Outputs
- Completed: Structurally validated canonical datasets and supporting methodology.
- Next: Research-specific analytical feature tables and reproducible Python/SQL analysis.
- Planned: Spending-efficiency comparisons, CBA and luxury-tax studies, selected roster and compensation case studies, and public-facing research visualizations and write-ups.
This repository is an ongoing analytical research project. Findings and interpretations will be added as Phase B and Phase C progress.