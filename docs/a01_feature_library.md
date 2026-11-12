A01 — Franchise-Season Feature Inventory
Status: v2A Groups A–D implemented; v2B/v2C/v3/v4 pending
Source of truth: notebooks/A01 Franchise Season Build.ipynb (uploaded notebook, 52 cells / 50 code cells)
Scope: 776 franchise-season observations, 2000–01 through 2025–26
Feature count: 89 engineered columns (excludes inherited canonical and joined context columns)
Document version: 0.2 — 2026-11-11
This inventory was reconstructed from the actual notebook SQL and the Group D audit results shared in conversation. Coverage for some earlier features is inferred from source completeness; where per-feature audits were not available, counts should be confirmed from the running DuckDB view. It does not claim independent external verification.

Architecture and lineage
- Grain/key: one (franchise_id, season) record; 776 rows, 776 unique keys in the final Group D audit.
- C04 team_seasons supplies the base grain and team financial/performance fields.
- C01 seasons supplies season salary-cap, luxury-tax and apron reference values and CBA regime.
- C10 team_apron_seasons supplies apron allocations, thresholds, flags and source metadata.
- DuckDB view chain: a01_base → a01_v1a → a01_v1b → a01_v1c → a01_v2a_financial → a01_v2a_competitive → a01_v2a_efficiency → a01_v2a_exposure.
- Views are TEMP VIEWs: they must be recreated by running the notebook in a new DuckDB session. No canonical CSVs are modified.
Inventory overview
Stage	Added features	Cumulative	Purpose
v1A	5	5	Financial normalization
v1B	6	11	Threshold distance and tax/cost ratios
v1C	9	20	Competition outcomes and cost per win
v2A-A	25	45	Financial position
v2A-B	6	51	Competitive position
v2A-C	12	63	Spending efficiency
v2A-D	26	89	Financial exposure


Feature-by-feature dictionary
Coverage is the observed non-null count for Group D, and an expected count based on source coverage for v1/v2A A–C unless separately audited. Formula summaries reflect the notebook SQL; NULLIF/`CASE` and window partitioning details remain in the source notebook.
v1A — 5 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
1	payroll_to_cap_ratio	Reported team payroll / season salary cap	Notebook cell 11: SQL alias `payroll_to_cap_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL
2	cap_hit_to_cap_ratio	Reported cap hit / season salary cap	Notebook cell 11: SQL alias `cap_hit_to_cap_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL
3	taxable_payroll_to_tax_ratio	Taxable payroll / league luxury-tax threshold	Notebook cell 11: SQL alias `taxable_payroll_to_tax_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL
4	first_apron_allocation_ratio	Apron allocations / first-apron threshold	Notebook cell 11: SQL alias `first_apron_allocation_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL
5	second_apron_allocation_ratio	Apron allocations / second-apron threshold, when applicable	Notebook cell 11: SQL alias `second_apron_allocation_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL


v1B — 6 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
6	tax_threshold_distance	Taxable payroll minus luxury-tax threshold	Notebook cell 19: SQL alias `tax_threshold_distance`	See source-field coverage	CASE/NULLIF rules in notebook SQL
7	first_apron_distance	Apron allocations minus first-apron threshold	Notebook cell 19: SQL alias `first_apron_distance`	See source-field coverage	CASE/NULLIF rules in notebook SQL
8	second_apron_distance	Apron allocations minus second-apron threshold when applicable	Notebook cell 19: SQL alias `second_apron_distance`	See source-field coverage	CASE/NULLIF rules in notebook SQL
9	above_tax_threshold	Taxable payroll exceeds league luxury-tax threshold (null if missing)	Notebook cell 19: SQL alias `above_tax_threshold`	See source-field coverage	CASE/NULLIF rules in notebook SQL
10	tax_paid_to_payroll_ratio	Observed actual tax paid / reported team payroll	Notebook cell 19: SQL alias `tax_paid_to_payroll_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL
11	estimated_cost_to_taxable_ratio	Source total estimated cost / taxable payroll	Notebook cell 19: SQL alias `estimated_cost_to_taxable_ratio`	See source-field coverage	CASE/NULLIF rules in notebook SQL


v1C — 9 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
12	games_played	Wins plus losses	Notebook cell 26: SQL alias `games_played`	See source-field coverage	CASE/NULLIF rules in notebook SQL
13	reached_second_round	Playoff advancement at least round_score 2	Notebook cell 26: SQL alias `reached_second_round`	See source-field coverage	CASE/NULLIF rules in notebook SQL
14	reached_conference_finals	Playoff advancement at least round_score 3	Notebook cell 26: SQL alias `reached_conference_finals`	See source-field coverage	CASE/NULLIF rules in notebook SQL
15	reached_nba_finals	Playoff advancement at least round_score 4	Notebook cell 26: SQL alias `reached_nba_finals`	See source-field coverage	CASE/NULLIF rules in notebook SQL
16	won_championship	Four playoff series won	Notebook cell 26: SQL alias `won_championship`	See source-field coverage	CASE/NULLIF rules in notebook SQL
17	wins_above_pythagorean	Wins minus Pythagorean expected wins	Notebook cell 26: SQL alias `wins_above_pythagorean`	See source-field coverage	CASE/NULLIF rules in notebook SQL
18	payroll_per_win	Reported payroll / wins	Notebook cell 26: SQL alias `payroll_per_win`	See source-field coverage	CASE/NULLIF rules in notebook SQL
19	taxable_payroll_per_win	Taxable payroll / wins	Notebook cell 26: SQL alias `taxable_payroll_per_win`	See source-field coverage	CASE/NULLIF rules in notebook SQL
20	estimated_cost_per_win	Source total estimated cost / wins	Notebook cell 26: SQL alias `estimated_cost_per_win`	See source-field coverage	CASE/NULLIF rules in notebook SQL


v2A-A — 25 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
21	payroll_rank	Season spending rank; highest = 1	RANK DESC(team_payroll) by season	776	Ranks/tiles exclude null source rows
22	payroll_percentile	Season-relative spending percentile	PERCENT_RANK ASC(team_payroll) by season, excluding NULL	776	Ranks/tiles exclude null source rows
23	payroll_to_league_median	Spending / season median	team_payroll / MEDIAN(team_payroll) by season	776	Ranks/tiles exclude null source rows
24	payroll_vs_league_median	Difference from season median	team_payroll - MEDIAN(team_payroll) by season	776	Ranks/tiles exclude null source rows
25	payroll_to_league_mean	Spending / season mean	team_payroll / AVG(team_payroll) by season	776	Ranks/tiles exclude null source rows
26	payroll_vs_league_mean	Difference from season mean	team_payroll - AVG(team_payroll) by season	776	Ranks/tiles exclude null source rows
27	payroll_quartile	Spending quartile; Q1 highest	NTILE(4) DESC(team_payroll), franchise_id by season	776	Ranks/tiles exclude null source rows
28	payroll_share_of_league_total	Share of season total	team_payroll / SUM(team_payroll) by season	776	Ranks/tiles exclude null source rows
29	taxable_payroll_rank	Season spending rank; highest = 1	RANK DESC(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
30	taxable_payroll_percentile	Season-relative spending percentile	PERCENT_RANK ASC(taxable_payroll) by season, excluding NULL	450	Ranks/tiles exclude null source rows
31	taxable_payroll_to_league_median	Spending / season median	taxable_payroll / MEDIAN(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
32	taxable_payroll_vs_league_median	Difference from season median	taxable_payroll - MEDIAN(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
33	taxable_payroll_to_league_mean	Spending / season mean	taxable_payroll / AVG(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
34	taxable_payroll_vs_league_mean	Difference from season mean	taxable_payroll - AVG(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
35	taxable_payroll_quartile	Spending quartile; Q1 highest	NTILE(4) DESC(taxable_payroll), franchise_id by season	450	Ranks/tiles exclude null source rows
36	taxable_payroll_share_of_league_total	Share of season total	taxable_payroll / SUM(taxable_payroll) by season	450	Ranks/tiles exclude null source rows
37	apron_allocations_rank	Season spending rank; highest = 1	RANK DESC(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
38	apron_allocations_percentile	Season-relative spending percentile	PERCENT_RANK ASC(total_apron_allocations) by season, excluding NULL	450	Ranks/tiles exclude null source rows
39	apron_allocations_to_league_median	Spending / season median	total_apron_allocations / MEDIAN(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
40	apron_allocations_vs_league_median	Difference from season median	total_apron_allocations - MEDIAN(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
41	apron_allocations_to_league_mean	Spending / season mean	total_apron_allocations / AVG(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
42	apron_allocations_vs_league_mean	Difference from season mean	total_apron_allocations - AVG(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
43	apron_allocations_quartile	Spending quartile; Q1 highest	NTILE(4) DESC(total_apron_allocations), franchise_id by season	450	Ranks/tiles exclude null source rows
44	apron_allocations_share_of_league_total	Share of season total	total_apron_allocations / SUM(total_apron_allocations) by season	450	Ranks/tiles exclude null source rows
45	salary_cap_hit_rank	Salary-cap-hit league rank	RANK DESC(salary_cap_hit) by season	776	Source cap hit equals team payroll for all 776 observed rows


v2A-B — 6 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
46	win_pct_rank	League win percentage rank	RANK DESC(win_pct) by season	776	—
47	win_pct_quartile	Competitive quartile; Q1 strongest	NTILE(4) DESC(win_pct), franchise_id by season	776	—
48	games_above_500	Conventional wins minus losses	wins - losses	776	—
49	wins_vs_league_mean	Wins above/below league average	wins - AVG(wins) by season	776	—
50	wins_vs_league_median	Wins above/below league median	wins - MEDIAN(wins) by season	776	—
51	playoff_advancement_rank	Postseason progression rank; non-playoff teams rank at -1 score	RANK DESC(CASE champion THEN 5 ELSE COALESCE(round_score,-1)) by season	776	—


v2A-C — 12 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
52	payroll_rank_win_pct_gap	Spending rank minus winning rank; positive suggests outperformance	payroll_rank - win_pct_rank	776	Peer average includes focal team
53	payroll_quartile_win_pct_gap	Spending quartile minus winning quartile	payroll_quartile - win_pct_quartile	776	Peer average includes focal team
54	payroll_normalized_efficiency	Relative win percentage per relative spending	(win_pct / season AVG(win_pct)) / (team_payroll / season MEDIAN(team_payroll))	776	Peer average includes focal team
55	win_pct_vs_payroll_quartile_mean	Win percentage relative to same spending quartile	win_pct - AVG(win_pct) OVER (PARTITION BY season, payroll_quartile)	776	Peer average includes focal team
56	taxable_payroll_rank_win_pct_gap	Spending rank minus winning rank; positive suggests outperformance	taxable_payroll_rank - win_pct_rank	450	Peer average includes focal team
57	taxable_payroll_quartile_win_pct_gap	Spending quartile minus winning quartile	taxable_payroll_quartile - win_pct_quartile	450	Peer average includes focal team
58	taxable_payroll_normalized_efficiency	Relative win percentage per relative spending	(win_pct / season AVG(win_pct)) / (taxable_payroll / season MEDIAN(taxable_payroll))	450	Peer average includes focal team
59	win_pct_vs_taxable_payroll_quartile_mean	Win percentage relative to same spending quartile	win_pct - AVG(win_pct) OVER (PARTITION BY season, taxable_payroll_quartile)	450	Peer average includes focal team
60	apron_allocations_rank_win_pct_gap	Spending rank minus winning rank; positive suggests outperformance	apron_allocations_rank - win_pct_rank	450	Peer average includes focal team
61	apron_allocations_quartile_win_pct_gap	Spending quartile minus winning quartile	apron_allocations_quartile - win_pct_quartile	450	Peer average includes focal team
62	apron_allocations_normalized_efficiency	Relative win percentage per relative spending	(win_pct / season AVG(win_pct)) / (total_apron_allocations / season MEDIAN(total_apron_allocations))	450	Peer average includes focal team
63	win_pct_vs_apron_allocations_quartile_mean	Win percentage relative to same spending quartile	win_pct - AVG(win_pct) OVER (PARTITION BY season, apron_allocations_quartile)	450	Peer average includes focal team


v2A-D — 26 features
#	Feature	Definition	SQL logic / source	Non-null coverage	Notes
64	estimated_tax_to_taxable_payroll_ratio	Estimated tax bill relative to taxable payroll	estimated_tax_bill / NULLIF(taxable_payroll, 0)	94	—
65	estimated_tax_to_payroll_ratio	Estimated tax bill relative to team payroll	estimated_tax_bill / NULLIF(team_payroll, 0)	94	—
66	tax_paid_league_rank	Actual tax rank across observed teams, including zero taxpayers	CASE WHEN tax_paid IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY tax_paid DESC NULLS LAST ) END	268	—
67	tax_paid_taxpayer_rank	Actual tax rank among positive-tax teams	CASE WHEN tax_paid > 0 THEN RANK() OVER ( PARTITION BY season, (tax_paid > 0) ORDER BY tax_paid DESC NULLS LAST ) END	72	—
68	tax_paid_league_share	Franchise share of observed season tax paid	CASE WHEN tax_paid IS NOT NULL THEN tax_paid / NULLIF(SUM(tax_paid) OVER (PARTITION BY season), 0) END	268	—
69	estimated_tax_bill_rank	Estimated bill rank among observed bills	CASE WHEN estimated_tax_bill IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY estimated_tax_bill DESC NULLS LAST ) END	94	—
70	estimated_tax_bill_observed_share	Share of sum of observed estimated bills	CASE WHEN estimated_tax_bill IS NOT NULL THEN estimated_tax_bill / NULLIF( SUM(estimated_tax_bill) OVER (PARTITION BY season), 0 ) END	94	—
71	total_estimated_cost_to_payroll_ratio	Source estimated cost relative to reported payroll	total_estimated_cost / NULLIF(team_payroll, 0)	450	—
72	total_estimated_cost_to_taxable_payroll_ratio	Source estimated cost relative to taxable payroll	total_estimated_cost / NULLIF(taxable_payroll, 0)	450	—
73	total_estimated_cost_rank	Source estimated-cost season rank	CASE WHEN total_estimated_cost IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY total_estimated_cost DESC NULLS LAST ) END	450	—
74	total_estimated_cost_percentile	Source estimated-cost season percentile	CASE WHEN total_estimated_cost IS NOT NULL THEN PERCENT_RANK() OVER ( PARTITION BY season, (total_estimated_cost IS NOT NULL) ORDER BY total_estimated_cost ASC NULLS LAST ) END	450	—
75	total_estimated_cost_to_league_median	Estimated cost divided by season median	total_estimated_cost / NULLIF( MEDIAN(total_estimated_cost) OVER (PARTITION BY season), 0 )	450	—
76	total_estimated_cost_to_league_mean	Estimated cost divided by season mean	total_estimated_cost / NULLIF( AVG(total_estimated_cost) OVER (PARTITION BY season), 0 )	450	—
77	estimated_cost_premium_over_taxable_payroll	Estimated cost minus taxable payroll, divided by taxable payroll	(total_estimated_cost - taxable_payroll) / NULLIF(taxable_payroll, 0)	450	—
78	tax_threshold_distance_pct	Signed tax-threshold distance as fraction of threshold	taxable_payroll_to_tax_ratio - 1	450	—
79	first_apron_distance_pct	Signed first-apron distance as fraction of threshold	first_apron_allocation_ratio - 1	450	—
80	second_apron_distance_pct	Signed second-apron distance as fraction of threshold	second_apron_allocation_ratio - 1	90	—
81	financial_exposure_category	Descriptive accounting-position category (not verified official legal status)	CASE WHEN taxable_payroll IS NULL OR league_luxury_tax_threshold IS NULL THEN NULL WHEN second_apron_applicable IS TRUE AND second_apron_threshold IS NOT NULL AND total_apron_allocations >= second_apron_threshold THEN 'at_or_above_second_apron' WHEN first_apron_threshold IS NOT NULL AND total_apron_allocations >= first_apron_threshold THEN 'at_or_above_first_apron' WHEN taxable_payroll >= league_luxury_tax_threshold THEN 'at_or_above_tax_threshold' ELSE 'below_tax_threshold' END	450	—
82	tax_paid_vs_league_mean	Actual tax paid minus observed league mean	CASE WHEN tax_paid IS NOT NULL THEN tax_paid - AVG(tax_paid) OVER (PARTITION BY season) END	268	—
83	tax_paid_vs_league_median	Actual tax paid minus observed league median	CASE WHEN tax_paid IS NOT NULL THEN tax_paid - MEDIAN(tax_paid) OVER (PARTITION BY season) END	268	—
84	tax_paid_vs_taxpayer_mean	Actual tax minus positive-taxpayer mean	CASE WHEN tax_paid > 0 THEN tax_paid - AVG(tax_paid) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END	72	—
85	tax_paid_vs_taxpayer_median	Actual tax minus positive-taxpayer median	CASE WHEN tax_paid > 0 THEN tax_paid - MEDIAN(tax_paid) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END	72	—
86	tax_paid_percentile	Actual tax percentile among observed teams	CASE WHEN tax_paid IS NOT NULL THEN PERCENT_RANK() OVER ( PARTITION BY season, (tax_paid IS NOT NULL) ORDER BY tax_paid ASC NULLS LAST ) END	268	—
87	tax_paid_taxpayer_percentile	Actual tax percentile among positive-tax teams	CASE WHEN tax_paid > 0 THEN PERCENT_RANK() OVER ( PARTITION BY season, (tax_paid > 0) ORDER BY tax_paid ASC NULLS LAST ) END	72	—
88	taxpayer_share_of_league	Fraction of franchises with positive tax when complete season observed	CASE WHEN COUNT(tax_paid) OVER (PARTITION BY season) = COUNT() OVER (PARTITION BY season) THEN COUNT() FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) * 1.0 / NULLIF(COUNT(*) OVER (PARTITION BY season), 0) END	268	—
89	tax_burden_vs_taxpayer_median	Tax-paid/payroll ratio minus taxpayer median ratio	CASE WHEN tax_paid > 0 THEN tax_paid_to_payroll_ratio - MEDIAN(tax_paid_to_payroll_ratio) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END	72	—


Coverage and validation evidence
Structural
- Group D final audit: 776 rows and 776 distinct (franchise_id, season) keys.
- 26 new Group D columns reported; feature counts sum to 89.
Financial coverage
- tax_paid: 268 non-null records, seasons 2002–03 through 2010–11; 72 positive tax observations and 196 confirmed zeros.
- estimated_tax_bill: 94 non-null records, 2011–12 through 2025–26.
- taxable_payroll, total_apron_allocations, and total_estimated_cost: 450 non-null records, 2011–12 through 2025–26.
- Second-apron percentage distance: 90 non-null records in the applicable three-season period.
- total_estimated_cost = taxable_payroll + COALESCE(estimated_tax_bill, 0) had 0 mismatches across 450 observed cost records (audit tolerance > 1); 356 of those records lack an estimated tax bill.
- Group D arithmetic and missing-rank checks: nine reported error counters, all 0.
- Source tax data do not overlap in time with the modern taxable-payroll observations; avoid joining them as if jointly observed.
Important interpretation safeguards
1. team_payroll, salary_cap_hit, taxable_payroll, and total_apron_allocations represent potentially different accounting concepts. The first two happen to match in all 776 observed C04 rows.
2. total_estimated_cost is source-defined estimated financial cost, not verified actual cash expenditure. A missing estimated bill does not prove zero tax liability.
3. Threshold-derived financial_exposure_category is a descriptive proxy based on available numerators and thresholds, not an official NBA regulatory ruling. Hard-cap flags and transaction triggers require source-semantic verification.
4. tax_paid is actual reported historical tax; estimated_tax_bill is a separate estimated metric. Do not merge without explicitly defining comparability.
5. Ranks, quartiles and percentiles are relative to teams within each season; ties and missing observations affect interpretation.
6. Peer-group mean in Group C includes the focal team; this is descriptive, not leave-one-out benchmarking.
7. playoff_advancement_rank uses a score mapping of champion=5, other playoff round_score, and non-playoff=-1; verify postseason ranking interpretation before research use.
8. Cross-season analysis should prefer win_pct where season lengths differ.
Code-review findings to resolve at next checkpoint
- Exact duplicate engineered formula: estimated_cost_to_taxable_ratio (feature 11, v1B) and total_estimated_cost_to_taxable_payroll_ratio (feature 72, Group D) both compute total_estimated_cost / NULLIF(taxable_payroll,0). The 89-column count is correct as implemented, but there are only 88 distinct engineered definitions if this duplicate is treated as one. Decide whether to retain a documented alias for compatibility or remove the later duplicate in a future revision; do not silently renumber prior checkpoints.
- Outdated notebook comment: Group D cell says "Creates 27 new features" while its assertion and actual list correctly specify 26. Correct the comment when next editing notebook; no rebuild needed.
- Source-sensitive regulatory labels: Reconcile salary definitions and applicable dates before using threshold categories to claim official apron/tax status.
- Potential duplicate rank: salary_cap_hit_rank and payroll_rank will coincide wherever C04 cap hit equals team payroll; currently this is all 776 rows. Both may be useful as explicitly named aliases but should not be treated as independent signals.
- v1 coverage values: Verify exact non-null counts for each v1 feature against the final view; this draft intentionally avoids inventing those counts.
Deferred features / scope control
- Nearest regulatory threshold; requires clear era-specific applicability rules.
- Hard-cap trigger type/count; requires transaction-level evidence not present in current A01 source layer.
- Actual tax divided by taxable payroll; historical non-overlap prevents observed calculation.
- Modeling-specific features, targets and causal estimands belong to research-specific downstream datasets, not mandatory A01 baseline.
Remaining A01 plan
- v2B: Year-over-year payroll, performance, rank, efficiency, and exposure changes; require consecutive-season checks.
- v2C: Three- and five-season rolling summaries, continuity/availability flags, and multi-year spending/performance measures.
- v3: Financial/competitive states and transition flags using source-supported, historically applicable definitions.
- v4: Final reproducibility, key/coverage/semantic audits, dictionary completion, final artifact freeze.
- A02: Separate player-franchise-season analytical library; do not combine distinct grains into A01.
Reproducibility checklist
- [x] Notebook source contains the SQL for all 89 engineered columns.
- [x] Group D structural and formula audits reported passing results.
- [ ] Confirm notebook executes top-to-bottom in a fresh kernel and reconstructs the final view.
- [ ] Generate exact non-null counts for all 89 features from the final view.
- [ ] Resolve/document duplicate formula and duplicate rank aliases.
- [ ] Validate external accounting semantics for threshold-sensitive interpretations.
- [ ] Add v2B/v2C/v3 definitions and results as completed.
- [ ] Finalize README and source methodology after A01/A02, as planned.