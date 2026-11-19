# A01 — Franchise-Season Feature Library
**Version:** v5 — CSV-reconciled documentation, 2026-11-18
**Status:** 210 persisted fields reconciled to docs/a01_feature_library.csv: 73 inherited/joined and 137 engineered. Observed non-null counts below are taken from the finalized CSV; semantic details follow the build notebook and documented assumptions.

## Dataset and lineage

- **Grain:** one row per `(franchise_id, season)`.
- **Scope:** 776 franchise-season records, 26 seasons (2000–01 through 2025–26), 30 franchises; 210 total persisted columns = 73 inherited/joined columns + 137 engineered columns.
- **Canonical inputs:** C04 team_seasons, C01 seasons, C10 team_apron_seasons; join logic is in notebook cell 9.
- **Construction:** `notebooks/a01_franchise_season_build.ipynb`; final DuckDB view `a01_v3_transitions`; exported `data/analytical/a01_franchise_season.parquet` (ZSTD compression).
- **Rebuild:** execute notebook construction cells in order; TEMP VIEWs are session-scoped. The Parquet export is a snapshot, not a replacement for reproducible SQL.
- **Coverage authority:** `docs/a01_feature_library.csv` is the authoritative field-level inventory for data types, non-null counts, coverage percentages, source dependencies, and limitations. The engineered-feature counts below were reconciled against that CSV (776 rows).

## Feature-stage summary

| Stage | New engineered columns | Cumulative engineered columns |
|---|---:|---:|
| v1A — Financial normalization | 5 | 5 |
| v1B — Threshold exposure | 6 | 11 |
| v1C — Competitive outcomes | 9 | 20 |
| v2A Group A — Financial position | 25 | 45 |
| v2A Group B — Competitive position | 6 | 51 |
| v2A Group C — Spending efficiency | 12 | 63 |
| v2A Group D — Financial exposure | 26 | 89 |
| v2B — Year-over-year change | 14 | 103 |
| v2C — Multiyear trends and persistence | 22 | 125 |
| v3 — State transitions | 12 | 137 |


**Inventory accounting:** 73 inherited/joined fields + 137 engineered fields = **210 persisted columns** across 776 franchise–season observations. The 137 engineered features are organized by the original A01 build stages; the inherited/joined fields are inventoried separately below.


## Analytical feature dictionary

Formulas below summarize the supplied CSV and A01 documentation. The **notebook SQL is authoritative** for exact CASE/NULLIF handling, ranking partitions, and tie behavior; notebook-cell references are retained where the documentation does not supply an explicit expression. Non-null coverage is measured out of **776 franchise–season rows**, not the number of nonzero or TRUE values.

### v1A — Financial normalization

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 1 | `payroll_to_cap_ratio` | Reported team payroll / season salary cap | Notebook cell 11: SQL alias payroll_to_cap_ratio | 776 | CASE/NULLIF rules in notebook SQL |
| 2 | `cap_hit_to_cap_ratio` | Reported cap hit / season salary cap | Notebook cell 11: SQL alias cap_hit_to_cap_ratio | 776 | CASE/NULLIF rules in notebook SQL |
| 3 | `taxable_payroll_to_tax_ratio` | Taxable payroll / league luxury-tax threshold | Notebook cell 11: SQL alias taxable_payroll_to_tax_ratio | 450 | CASE/NULLIF rules in notebook SQL |
| 4 | `first_apron_allocation_ratio` | Apron allocations / first-apron threshold | Notebook cell 11: SQL alias first_apron_allocation_ratio | 450 | CASE/NULLIF rules in notebook SQL |
| 5 | `second_apron_allocation_ratio` | Apron allocations / second-apron threshold, when applicable | Notebook cell 11: SQL alias second_apron_allocation_ratio | 90 | CASE/NULLIF rules in notebook SQL |


### v1B — Threshold exposure

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 6 | `tax_threshold_distance` | Taxable payroll minus luxury-tax threshold | Notebook cell 19: SQL alias tax_threshold_distance | 450 | CASE/NULLIF rules in notebook SQL |
| 7 | `first_apron_distance` | Apron allocations minus first-apron threshold | Notebook cell 19: SQL alias first_apron_distance | 450 | CASE/NULLIF rules in notebook SQL |
| 8 | `second_apron_distance` | Apron allocations minus second-apron threshold when applicable | Notebook cell 19: SQL alias second_apron_distance | 90 | CASE/NULLIF rules in notebook SQL |
| 9 | `above_tax_threshold` | Taxable payroll exceeds league luxury-tax threshold (null if missing) | Notebook cell 19: SQL alias above_tax_threshold | 450 | CASE/NULLIF rules in notebook SQL |
| 10 | `tax_paid_to_payroll_ratio` | Observed actual tax paid / reported team payroll | Notebook cell 19: SQL alias tax_paid_to_payroll_ratio | 268 | CASE/NULLIF rules in notebook SQL |
| 11 | `estimated_cost_to_taxable_ratio` | Source total estimated cost / taxable payroll | Notebook cell 19: SQL alias estimated_cost_to_taxable_ratio | 450 | CASE/NULLIF rules in notebook SQL |


### v1C — Competitive outcomes

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 12 | `games_played` | Wins plus losses | Notebook cell 26: SQL alias games_played | 776 | CASE/NULLIF rules in notebook SQL |
| 13 | `reached_second_round` | Playoff advancement at least round_score 2 | Notebook cell 26: SQL alias reached_second_round | 776 | CASE/NULLIF rules in notebook SQL |
| 14 | `reached_conference_finals` | Playoff advancement at least round_score 3 | Notebook cell 26: SQL alias reached_conference_finals | 776 | CASE/NULLIF rules in notebook SQL |
| 15 | `reached_nba_finals` | Playoff advancement at least round_score 4 | Notebook cell 26: SQL alias reached_nba_finals | 776 | CASE/NULLIF rules in notebook SQL |
| 16 | `won_championship` | Four playoff series won | Notebook cell 26: SQL alias won_championship | 776 | CASE/NULLIF rules in notebook SQL |
| 17 | `wins_above_pythagorean` | Wins minus Pythagorean expected wins | Notebook cell 26: SQL alias wins_above_pythagorean | 776 | CASE/NULLIF rules in notebook SQL |
| 18 | `payroll_per_win` | Reported payroll / wins | Notebook cell 26: SQL alias payroll_per_win | 776 | CASE/NULLIF rules in notebook SQL |
| 19 | `taxable_payroll_per_win` | Taxable payroll / wins | Notebook cell 26: SQL alias taxable_payroll_per_win | 450 | CASE/NULLIF rules in notebook SQL |
| 20 | `estimated_cost_per_win` | Source total estimated cost / wins | Notebook cell 26: SQL alias estimated_cost_per_win | 450 | CASE/NULLIF rules in notebook SQL |


### v2A Group A — Financial position

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 21 | `payroll_rank` | Season spending rank; highest = 1 | RANK DESC(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 22 | `payroll_percentile` | Season-relative spending percentile | PERCENT_RANK ASC(team_payroll) by season, excluding NULL | 776 | Ranks/tiles exclude null source rows |
| 23 | `payroll_to_league_median` | Spending / season median | team_payroll / MEDIAN(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 24 | `payroll_vs_league_median` | Difference from season median | team_payroll - MEDIAN(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 25 | `payroll_to_league_mean` | Spending / season mean | team_payroll / AVG(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 26 | `payroll_vs_league_mean` | Difference from season mean | team_payroll - AVG(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 27 | `payroll_quartile` | Spending quartile; Q1 highest | NTILE(4) DESC(team_payroll), franchise_id by season | 776 | Ranks/tiles exclude null source rows |
| 28 | `payroll_share_of_league_total` | Share of season total | team_payroll / SUM(team_payroll) by season | 776 | Ranks/tiles exclude null source rows |
| 29 | `taxable_payroll_rank` | Season spending rank; highest = 1 | RANK DESC(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 30 | `taxable_payroll_percentile` | Season-relative spending percentile | PERCENT_RANK ASC(taxable_payroll) by season, excluding NULL | 450 | Ranks/tiles exclude null source rows |
| 31 | `taxable_payroll_to_league_median` | Spending / season median | taxable_payroll / MEDIAN(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 32 | `taxable_payroll_vs_league_median` | Difference from season median | taxable_payroll - MEDIAN(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 33 | `taxable_payroll_to_league_mean` | Spending / season mean | taxable_payroll / AVG(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 34 | `taxable_payroll_vs_league_mean` | Difference from season mean | taxable_payroll - AVG(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 35 | `taxable_payroll_quartile` | Spending quartile; Q1 highest | NTILE(4) DESC(taxable_payroll), franchise_id by season | 450 | Ranks/tiles exclude null source rows |
| 36 | `taxable_payroll_share_of_league_total` | Share of season total | taxable_payroll / SUM(taxable_payroll) by season | 450 | Ranks/tiles exclude null source rows |
| 37 | `apron_allocations_rank` | Season spending rank; highest = 1 | RANK DESC(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 38 | `apron_allocations_percentile` | Season-relative spending percentile | PERCENT_RANK ASC(total_apron_allocations) by season, excluding NULL | 450 | Ranks/tiles exclude null source rows |
| 39 | `apron_allocations_to_league_median` | Spending / season median | total_apron_allocations / MEDIAN(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 40 | `apron_allocations_vs_league_median` | Difference from season median | total_apron_allocations - MEDIAN(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 41 | `apron_allocations_to_league_mean` | Spending / season mean | total_apron_allocations / AVG(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 42 | `apron_allocations_vs_league_mean` | Difference from season mean | total_apron_allocations - AVG(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 43 | `apron_allocations_quartile` | Spending quartile; Q1 highest | NTILE(4) DESC(total_apron_allocations), franchise_id by season | 450 | Ranks/tiles exclude null source rows |
| 44 | `apron_allocations_share_of_league_total` | Share of season total | total_apron_allocations / SUM(total_apron_allocations) by season | 450 | Ranks/tiles exclude null source rows |
| 45 | `salary_cap_hit_rank` | Salary-cap-hit league rank | RANK DESC(salary_cap_hit) by season | 776 | Source cap hit equals team payroll for all 776 observed rows |


### v2A Group B — Competitive position

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 46 | `win_pct_rank` | League win percentage rank | RANK DESC(win_pct) by season | 776 | — |
| 47 | `win_pct_quartile` | Competitive quartile; Q1 strongest | NTILE(4) DESC(win_pct), franchise_id by season | 776 | — |
| 48 | `games_above_500` | Conventional wins minus losses | wins - losses | 776 | — |
| 49 | `wins_vs_league_mean` | Wins above/below league average | wins - AVG(wins) by season | 776 | — |
| 50 | `wins_vs_league_median` | Wins above/below league median | wins - MEDIAN(wins) by season | 776 | — |
| 51 | `playoff_advancement_rank` | Postseason progression rank; non-playoff teams rank at -1 score | RANK DESC(CASE champion THEN 5 ELSE COALESCE(round_score,-1)) by season | 776 | — |


### v2A Group C — Spending efficiency

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 52 | `payroll_rank_win_pct_gap` | Spending rank minus winning rank; positive suggests outperformance | payroll_rank - win_pct_rank | 776 | Peer average includes focal team |
| 53 | `payroll_quartile_win_pct_gap` | Spending quartile minus winning quartile | payroll_quartile - win_pct_quartile | 776 | Peer average includes focal team |
| 54 | `payroll_normalized_efficiency` | Relative win percentage per relative spending | (win_pct / season AVG(win_pct)) / (team_payroll / season MEDIAN(team_payroll)) | 776 | Peer average includes focal team |
| 55 | `win_pct_vs_payroll_quartile_mean` | Win percentage relative to same spending quartile | win_pct - AVG(win_pct) OVER (PARTITION BY season, payroll_quartile) | 776 | Peer average includes focal team |
| 56 | `taxable_payroll_rank_win_pct_gap` | Spending rank minus winning rank; positive suggests outperformance | taxable_payroll_rank - win_pct_rank | 450 | Peer average includes focal team |
| 57 | `taxable_payroll_quartile_win_pct_gap` | Spending quartile minus winning quartile | taxable_payroll_quartile - win_pct_quartile | 450 | Peer average includes focal team |
| 58 | `taxable_payroll_normalized_efficiency` | Relative win percentage per relative spending | (win_pct / season AVG(win_pct)) / (taxable_payroll / season MEDIAN(taxable_payroll)) | 450 | Peer average includes focal team |
| 59 | `win_pct_vs_taxable_payroll_quartile_mean` | Win percentage relative to same spending quartile | win_pct - AVG(win_pct) OVER (PARTITION BY season, taxable_payroll_quartile) | 450 | Peer average includes focal team |
| 60 | `apron_allocations_rank_win_pct_gap` | Spending rank minus winning rank; positive suggests outperformance | apron_allocations_rank - win_pct_rank | 450 | Peer average includes focal team |
| 61 | `apron_allocations_quartile_win_pct_gap` | Spending quartile minus winning quartile | apron_allocations_quartile - win_pct_quartile | 450 | Peer average includes focal team |
| 62 | `apron_allocations_normalized_efficiency` | Relative win percentage per relative spending | (win_pct / season AVG(win_pct)) / (total_apron_allocations / season MEDIAN(total_apron_allocations)) | 450 | Peer average includes focal team |
| 63 | `win_pct_vs_apron_allocations_quartile_mean` | Win percentage relative to same spending quartile | win_pct - AVG(win_pct) OVER (PARTITION BY season, apron_allocations_quartile) | 450 | Peer average includes focal team |


### v2A Group D — Financial exposure

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 64 | `estimated_tax_to_taxable_payroll_ratio` | Estimated tax bill relative to taxable payroll | estimated_tax_bill / NULLIF(taxable_payroll, 0) | 94 | — |
| 65 | `estimated_tax_to_payroll_ratio` | Estimated tax bill relative to team payroll | estimated_tax_bill / NULLIF(team_payroll, 0) | 94 | — |
| 66 | `tax_paid_league_rank` | Actual tax rank across observed teams, including zero taxpayers | CASE WHEN tax_paid IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY tax_paid DESC NULLS LAST ) END | 268 | — |
| 67 | `tax_paid_taxpayer_rank` | Actual tax rank among positive-tax teams | CASE WHEN tax_paid > 0 THEN RANK() OVER ( PARTITION BY season, (tax_paid > 0) ORDER BY tax_paid DESC NULLS LAST ) END | 72 | — |
| 68 | `tax_paid_league_share` | Franchise share of observed season tax paid | CASE WHEN tax_paid IS NOT NULL THEN tax_paid / NULLIF(SUM(tax_paid) OVER (PARTITION BY season), 0) END | 268 | — |
| 69 | `estimated_tax_bill_rank` | Estimated bill rank among observed bills | CASE WHEN estimated_tax_bill IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY estimated_tax_bill DESC NULLS LAST ) END | 94 | — |
| 70 | `estimated_tax_bill_observed_share` | Share of sum of observed estimated bills | CASE WHEN estimated_tax_bill IS NOT NULL THEN estimated_tax_bill / NULLIF( SUM(estimated_tax_bill) OVER (PARTITION BY season), 0 ) END | 94 | — |
| 71 | `total_estimated_cost_to_payroll_ratio` | Source estimated cost relative to reported payroll | total_estimated_cost / NULLIF(team_payroll, 0) | 450 | — |
| 72 | `total_estimated_cost_to_taxable_payroll_ratio` | Source estimated cost relative to taxable payroll | total_estimated_cost / NULLIF(taxable_payroll, 0) | 450 | — |
| 73 | `total_estimated_cost_rank` | Source estimated-cost season rank | CASE WHEN total_estimated_cost IS NOT NULL THEN RANK() OVER ( PARTITION BY season ORDER BY total_estimated_cost DESC NULLS LAST ) END | 450 | — |
| 74 | `total_estimated_cost_percentile` | Source estimated-cost season percentile | CASE WHEN total_estimated_cost IS NOT NULL THEN PERCENT_RANK() OVER ( PARTITION BY season, (total_estimated_cost IS NOT NULL) ORDER BY total_estimated_cost ASC NULLS LAST ) END | 450 | — |
| 75 | `total_estimated_cost_to_league_median` | Estimated cost divided by season median | total_estimated_cost / NULLIF( MEDIAN(total_estimated_cost) OVER (PARTITION BY season), 0 ) | 450 | — |
| 76 | `total_estimated_cost_to_league_mean` | Estimated cost divided by season mean | total_estimated_cost / NULLIF( AVG(total_estimated_cost) OVER (PARTITION BY season), 0 ) | 450 | — |
| 77 | `estimated_cost_premium_over_taxable_payroll` | Estimated cost minus taxable payroll, divided by taxable payroll | (total_estimated_cost - taxable_payroll) / NULLIF(taxable_payroll, 0) | 450 | — |
| 78 | `tax_threshold_distance_pct` | Signed tax-threshold distance as fraction of threshold | taxable_payroll_to_tax_ratio - 1 | 450 | — |
| 79 | `first_apron_distance_pct` | Signed first-apron distance as fraction of threshold | first_apron_allocation_ratio - 1 | 450 | — |
| 80 | `second_apron_distance_pct` | Signed second-apron distance as fraction of threshold | second_apron_allocation_ratio - 1 | 90 | — |
| 81 | `financial_exposure_category` | Descriptive accounting-position category (not verified official legal status) | CASE WHEN taxable_payroll IS NULL OR league_luxury_tax_threshold IS NULL THEN NULL WHEN second_apron_applicable IS TRUE AND second_apron_threshold IS NOT NULL AND total_apron_allocations >= second_apron_threshold THEN 'at_or_above_second_apron' WHEN first_apron_threshold IS NOT NULL AND total_apron_allocations >= first_apron_threshold THEN 'at_or_above_first_apron' WHEN taxable_payroll >= league_luxury_tax_threshold THEN 'at_or_above_tax_threshold' ELSE 'below_tax_threshold' END | 450 | — |
| 82 | `tax_paid_vs_league_mean` | Actual tax paid minus observed league mean | CASE WHEN tax_paid IS NOT NULL THEN tax_paid - AVG(tax_paid) OVER (PARTITION BY season) END | 268 | — |
| 83 | `tax_paid_vs_league_median` | Actual tax paid minus observed league median | CASE WHEN tax_paid IS NOT NULL THEN tax_paid - MEDIAN(tax_paid) OVER (PARTITION BY season) END | 268 | — |
| 84 | `tax_paid_vs_taxpayer_mean` | Actual tax minus positive-taxpayer mean | CASE WHEN tax_paid > 0 THEN tax_paid - AVG(tax_paid) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END | 72 | — |
| 85 | `tax_paid_vs_taxpayer_median` | Actual tax minus positive-taxpayer median | CASE WHEN tax_paid > 0 THEN tax_paid - MEDIAN(tax_paid) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END | 72 | — |
| 86 | `tax_paid_percentile` | Actual tax percentile among observed teams | CASE WHEN tax_paid IS NOT NULL THEN PERCENT_RANK() OVER ( PARTITION BY season, (tax_paid IS NOT NULL) ORDER BY tax_paid ASC NULLS LAST ) END | 268 | — |
| 87 | `tax_paid_taxpayer_percentile` | Actual tax percentile among positive-tax teams | CASE WHEN tax_paid > 0 THEN PERCENT_RANK() OVER ( PARTITION BY season, (tax_paid > 0) ORDER BY tax_paid ASC NULLS LAST ) END | 72 | — |
| 88 | `taxpayer_share_of_league` | Fraction of franchises with positive tax when complete season observed | CASE WHEN COUNT(tax_paid) OVER (PARTITION BY season) = COUNT() OVER (PARTITION BY season) THEN COUNT() FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) * 1.0 / NULLIF(COUNT(*) OVER (PARTITION BY season), 0) END | 268 | — |
| 89 | `tax_burden_vs_taxpayer_median` | Tax-paid/payroll ratio minus taxpayer median ratio | CASE WHEN tax_paid > 0 THEN tax_paid_to_payroll_ratio - MEDIAN(tax_paid_to_payroll_ratio) FILTER ( WHERE tax_paid > 0 ) OVER (PARTITION BY season) END | 72 | — |


### v2B — Year-over-year change

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 90 | `team_payroll_yoy_growth` | Team payroll growth relative to previous season | team_payroll / previous team_payroll - 1 | 745 | Requires consecutive prior season and nonmissing inputs. |
| 91 | `taxable_payroll_yoy_growth` | Taxable payroll growth relative to previous season | taxable_payroll / previous taxable_payroll - 1 | 420 | Requires consecutive prior season and nonmissing inputs. |
| 92 | `apron_allocations_yoy_growth` | Apron allocations growth relative to previous season | total_apron_allocations / previous allocations - 1 | 420 | Requires consecutive prior season and nonmissing inputs. |
| 93 | `salary_cap_yoy_growth` | Season salary-cap growth | salary_cap / previous salary_cap - 1 | 747 | Requires consecutive prior season and nonmissing inputs. |
| 94 | `tax_threshold_yoy_growth` | Season luxury-tax threshold growth | league_luxury_tax_threshold / previous threshold - 1 | 629 | Requires consecutive prior season and nonmissing inputs. |
| 95 | `first_apron_threshold_yoy_growth` | Season first-apron threshold growth | league_first_apron / previous first apron - 1 | 420 | Requires consecutive prior season and nonmissing inputs. |
| 96 | `second_apron_threshold_yoy_growth` | Season second-apron threshold growth | league_second_apron / previous second apron - 1 | 60 | Requires consecutive prior season and nonmissing inputs. |
| 97 | `payroll_to_cap_yoy_change` | Change in payroll-to-cap ratio, in ratio points | payroll_to_cap_ratio - previous ratio | 745 | Requires consecutive prior season and nonmissing inputs. |
| 98 | `taxable_to_tax_yoy_change` | Change in taxable-payroll-to-tax ratio, in ratio points | taxable_payroll_to_tax_ratio - previous ratio | 420 | Requires consecutive prior season and nonmissing inputs. |
| 99 | `first_apron_ratio_yoy_change` | Change in first-apron allocation ratio, in ratio points | first_apron_allocation_ratio - previous ratio | 420 | Requires consecutive prior season and nonmissing inputs. |
| 100 | `second_apron_ratio_yoy_change` | Change in second-apron allocation ratio, in ratio points | second_apron_allocation_ratio - previous ratio | 60 | Requires consecutive prior season and nonmissing inputs. |
| 101 | `win_pct_yoy_change` | Change in win percentage, in percentage-point units of 0–1 scale | win_pct - previous win_pct | 745 | Requires consecutive prior season and nonmissing inputs. |
| 102 | `payroll_rank_yoy_improvement` | Improvement in season payroll rank; positive means improved rank | previous payroll_rank - current payroll_rank | 745 | Requires consecutive prior season and nonmissing inputs. |
| 103 | `payroll_efficiency_yoy_change` | Change in normalized payroll efficiency | payroll_normalized_efficiency - previous efficiency | 745 | Requires consecutive prior season and nonmissing inputs. |


### v2C — Multiyear trends and persistence

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 104 | `avg_payroll_to_cap_3yr` | Trailing 3-season average of payroll_to_cap_ratio | AVG(payroll_to_cap_ratio) over 3 consecutive franchise seasons; require 3 nonmissing values | 714 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 105 | `avg_taxable_to_tax_3yr` | Trailing 3-season average of taxable_payroll_to_tax_ratio | AVG(taxable_payroll_to_tax_ratio) over 3 consecutive franchise seasons; require 3 nonmissing values | 390 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 106 | `avg_first_apron_ratio_3yr` | Trailing 3-season average of first_apron_allocation_ratio | AVG(first_apron_allocation_ratio) over 3 consecutive franchise seasons; require 3 nonmissing values | 390 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 107 | `avg_second_apron_ratio_3yr` | Trailing 3-season average of second_apron_allocation_ratio | AVG(second_apron_allocation_ratio) over 3 consecutive franchise seasons; require 3 nonmissing values | 30 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 108 | `avg_win_pct_3yr` | Trailing 3-season average of win_pct | AVG(win_pct) over 3 consecutive franchise seasons; require 3 nonmissing values | 714 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 109 | `avg_payroll_efficiency_3yr` | Trailing 3-season average of payroll_normalized_efficiency | AVG(payroll_normalized_efficiency) over 3 consecutive franchise seasons; require 3 nonmissing values | 714 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 110 | `playoff_appearances_3yr` | Number of seasons with made_playoffs=TRUE in trailing 3 seasons | SUM(CASE WHEN made_playoffs THEN 1 ELSE 0 END) over 3 consecutive seasons | 714 | Current season included; requires complete nonmissing window. |
| 111 | `second_round_appearances_3yr` | Number of seasons with reached_second_round=TRUE in trailing 3 seasons | SUM(CASE WHEN reached_second_round THEN 1 ELSE 0 END) over 3 consecutive seasons | 714 | Current season included; requires complete nonmissing window. |
| 112 | `conference_finals_appearances_3yr` | Number of seasons with reached_conference_finals=TRUE in trailing 3 seasons | SUM(CASE WHEN reached_conference_finals THEN 1 ELSE 0 END) over 3 consecutive seasons | 714 | Current season included; requires complete nonmissing window. |
| 113 | `tax_threshold_seasons_3yr` | Number of seasons at or above threshold (taxable_payroll_to_tax_ratio >= 1) in trailing 3 seasons | SUM(CASE WHEN taxable_payroll_to_tax_ratio >= 1 THEN 1 ELSE 0 END) over 3 consecutive seasons | 390 | Historical accounting proxy; not necessarily modern CBA legal status. |
| 114 | `first_apron_seasons_3yr` | Number of seasons at or above threshold (first_apron_allocation_ratio >= 1) in trailing 3 seasons | SUM(CASE WHEN first_apron_allocation_ratio >= 1 THEN 1 ELSE 0 END) over 3 consecutive seasons | 390 | Historical accounting proxy; not necessarily modern CBA legal status. |
| 115 | `second_apron_seasons_3yr` | Number of seasons at or above threshold (second_apron_allocation_ratio >= 1) in trailing 3 seasons | SUM(CASE WHEN second_apron_allocation_ratio >= 1 THEN 1 ELSE 0 END) over 3 consecutive seasons | 30 | Historical accounting proxy; not necessarily modern CBA legal status. |
| 116 | `avg_payroll_to_cap_6yr` | Trailing 6-season average of payroll_to_cap_ratio | AVG(payroll_to_cap_ratio) over 6 consecutive franchise seasons; require 6 nonmissing values | 624 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 117 | `avg_taxable_to_tax_6yr` | Trailing 6-season average of taxable_payroll_to_tax_ratio | AVG(taxable_payroll_to_tax_ratio) over 6 consecutive franchise seasons; require 6 nonmissing values | 300 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 118 | `avg_first_apron_ratio_6yr` | Trailing 6-season average of first_apron_allocation_ratio | AVG(first_apron_allocation_ratio) over 6 consecutive franchise seasons; require 6 nonmissing values | 300 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 119 | `avg_win_pct_6yr` | Trailing 6-season average of win_pct | AVG(win_pct) over 6 consecutive franchise seasons; require 6 nonmissing values | 624 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 120 | `avg_payroll_efficiency_6yr` | Trailing 6-season average of payroll_normalized_efficiency | AVG(payroll_normalized_efficiency) over 6 consecutive franchise seasons; require 6 nonmissing values | 624 | Current season included; incomplete or nonconsecutive windows return NULL. |
| 121 | `playoff_appearances_6yr` | Number of seasons with made_playoffs=TRUE in trailing 6 seasons | SUM(CASE WHEN made_playoffs THEN 1 ELSE 0 END) over 6 consecutive seasons | 624 | Current season included; requires complete nonmissing window. |
| 122 | `second_round_appearances_6yr` | Number of seasons with reached_second_round=TRUE in trailing 6 seasons | SUM(CASE WHEN reached_second_round THEN 1 ELSE 0 END) over 6 consecutive seasons | 624 | Current season included; requires complete nonmissing window. |
| 123 | `conference_finals_appearances_6yr` | Number of seasons with reached_conference_finals=TRUE in trailing 6 seasons | SUM(CASE WHEN reached_conference_finals THEN 1 ELSE 0 END) over 6 consecutive seasons | 624 | Current season included; requires complete nonmissing window. |
| 124 | `tax_threshold_seasons_6yr` | Number of seasons at or above threshold (taxable_payroll_to_tax_ratio >= 1) in trailing 6 seasons | SUM(CASE WHEN taxable_payroll_to_tax_ratio >= 1 THEN 1 ELSE 0 END) over 6 consecutive seasons | 300 | Historical accounting proxy; not necessarily modern CBA legal status. |
| 125 | `first_apron_seasons_6yr` | Number of seasons at or above threshold (first_apron_allocation_ratio >= 1) in trailing 6 seasons | SUM(CASE WHEN first_apron_allocation_ratio >= 1 THEN 1 ELSE 0 END) over 6 consecutive seasons | 300 | Historical accounting proxy; not necessarily modern CBA legal status. |


### v3 — State transitions

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 126 | `entered_tax_threshold` | Entered tax threshold state relative to preceding season | taxable_payroll_to_tax_ratio >= 1; previous FALSE and current TRUE | 420 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 127 | `exited_tax_threshold` | Exited tax threshold state relative to preceding season | taxable_payroll_to_tax_ratio >= 1; previous TRUE and current FALSE | 420 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 128 | `entered_first_apron` | Entered first apron state relative to preceding season | first_apron_allocation_ratio >= 1; previous FALSE and current TRUE | 420 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 129 | `exited_first_apron` | Exited first apron state relative to preceding season | first_apron_allocation_ratio >= 1; previous TRUE and current FALSE | 420 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 130 | `entered_second_apron` | Entered second apron state relative to preceding season | second_apron_allocation_ratio >= 1; previous FALSE and current TRUE | 60 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 131 | `exited_second_apron` | Exited second apron state relative to preceding season | second_apron_allocation_ratio >= 1; previous TRUE and current FALSE | 60 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 132 | `newly_qualified_playoffs` | Entered playoffs state relative to preceding season | made_playoffs; previous FALSE and current TRUE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 133 | `lost_playoff_qualification` | Exited playoffs state relative to preceding season | made_playoffs; previous TRUE and current FALSE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 134 | `newly_reached_second_round` | Entered second round state relative to preceding season | reached_second_round; previous FALSE and current TRUE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 135 | `failed_to_repeat_second_round` | Exited second round state relative to preceding season | reached_second_round; previous TRUE and current FALSE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 136 | `newly_reached_conference_finals` | Entered conference finals state relative to preceding season | reached_conference_finals; previous FALSE and current TRUE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |
| 137 | `failed_to_repeat_conference_finals` | Exited conference finals state relative to preceding season | reached_conference_finals; previous TRUE and current FALSE | 745 | Requires adjacent seasons and observed states in both; historical apron status is descriptive. |


## Design choices, deferrals, and interpretive limits

1. **Different accounting bases.** team_payroll, salary_cap_hit, taxable_payroll, and total_apron_allocations are distinct concepts. Observed equality of cap hit and payroll is not proof of conceptual equivalence.
2. **Actual versus estimated tax.** historical tax_paid and modern estimated_tax_bill differ in meaning and observed periods. Do not combine them as a homogeneous realized-cost series.
3. **Estimated total cost.** source-defined, not verified ownership cash expenditure; missing estimated tax is not evidence of zero liability.
4. **Historical CBA.** threshold crossings and apron states are descriptive calculations. Pre-modern first-apron comparisons do not imply the modern regulatory restrictions applied.
5. **Second apron.** applicability and source coverage are limited; v2C intentionally omits six-season second-apron averages/counts.
6. **Peer benchmarks.** season-relative ranks, quartiles, percentiles, and averages depend on observed teams; spending-quartile means include the focal team.
7. **Season comparability.** season length varies. Prefer win percentage rather than raw wins for cross-season comparisons.
8. **Time-series windows.** v2B requires adjacent seasons; v2C requires fully observed, consecutive 3- or 6-season windows; v3 requires adjacent, nonmissing states.
9. **Postseason.** playoff milestone Booleans follow notebook round_score/series-won definitions; rankings use champion=5, other playoff round_score, non-playoff=-1.
10. **Redundant columns.** estimated_cost_to_taxable_ratio and total_estimated_cost_to_taxable_payroll_ratio duplicate the same formula; salary_cap_hit_rank coincides with payroll_rank in the observed data. Retained as documented aliases, not independent signals.
11. **Storage normalization.** 11 rolling-count features were cast from DuckDB HUGEINT to Parquet BIGINT; verified within 3- or 6-season bounds.
12. **Export verification.** 776 rows, 210 columns, unique keys, identical missingness and exact non-floating-point values; floating-point differences no greater than 1.11e-16 in the observed comparison, and within 1e-12 in the final validation.

## Inherited and joined field inventory (73 columns)

The following fields are persisted in A01 but were not independently engineered in its feature stages. Field definitions, source dependencies, and missingness are maintained in `docs/a01_feature_library.csv`; the canonical table and original source methodology remain the authorities for source-level semantics. Coverage counts are non-null observations out of 776 franchise-seasons.

| Field | Origin | Definition | Non-null | Caveat |
|---|---|---|---:|---|
| `team_name` | Inherited C04 franchise-season | Historical franchise/team name used for the season record | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `wins` | Inherited C04 franchise-season | Regular-season wins | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Season lengths vary across years. |
| `losses` | Inherited C04 franchise-season | Regular-season losses | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Season lengths vary across years. |
| `win_pct` | Inherited C04 franchise-season | Regular-season winning percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Season lengths vary across years. |
| `games_behind` | Inherited C04 franchise-season | Standings games behind the leader | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `points_per_game` | Inherited C04 franchise-season | Team points scored per game | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `opponent_points_per_game` | Inherited C04 franchise-season | Opponent points allowed per game | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `simple_rating_system` | Inherited C04 franchise-season | Simple Rating System team strength estimate | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `conference` | Inherited C04 franchise-season | Conference affiliation for the season | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `season` | Inherited C04 franchise-season | NBA season identifier | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `franchise_id` | Inherited C04 franchise-season | Stable franchise identifier used as the franchise-season join key | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `league_rank` | Inherited C04 franchise-season | League standing or rank supplied by canonical source | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `average_age` | Inherited C04 franchise-season | Roster average age from team-season source | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `pythagorean_wins` | Inherited C04 franchise-season | Expected wins from point-scoring based Pythagorean model | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `pythagorean_losses` | Inherited C04 franchise-season | Expected losses from point-scoring based Pythagorean model | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `margin_of_victory` | Inherited C04 franchise-season | Average scoring margin per game | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `strength_of_schedule` | Inherited C04 franchise-season | Schedule strength estimate | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `offensive_rating` | Inherited C04 franchise-season | Estimated points scored per 100 possessions | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `defensive_rating` | Inherited C04 franchise-season | Estimated points allowed per 100 possessions | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `net_rating` | Inherited C04 franchise-season | Offensive rating minus defensive rating | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `pace` | Inherited C04 franchise-season | Estimated possessions per game | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `free_throw_rate` | Inherited C04 franchise-season | Team free-throw rate | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `three_point_attempt_rate` | Inherited C04 franchise-season | Share of field-goal attempts from three-point range | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `true_shooting_pct` | Inherited C04 franchise-season | Shooting efficiency accounting for field goals and free throws | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `off_eFG%` | Inherited C04 franchise-season | Offensive effective field-goal percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `off_TOV%` | Inherited C04 franchise-season | Offensive turnover percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `off_ORB%` | Inherited C04 franchise-season | Offensive offensive-rebound percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `off_FT/FGA` | Inherited C04 franchise-season | Offensive free throws per field-goal attempt | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `def_eFG%` | Inherited C04 franchise-season | Opponent effective field-goal percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `def_TOV%` | Inherited C04 franchise-season | Opponent turnover percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `def_DRB%` | Inherited C04 franchise-season | Defensive defensive-rebound percentage | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `def_FT/FGA` | Inherited C04 franchise-season | Opponent free throws per field-goal attempt | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `arena` | Inherited C04 franchise-season | Home arena name | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `attendance` | Inherited C04 franchise-season | Reported season attendance | 772 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `attendance_per_game` | Inherited C04 franchise-season | Reported attendance per home game | 772 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `seed` | Inherited C04 franchise-season | Postseason seed | 416 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `playoff_series_won` | Inherited C04 franchise-season | Number of postseason series won | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `round_score` | Inherited C04 franchise-season | Numeric postseason advancement score | 416 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `furthest_round` | Inherited C04 franchise-season | Furthest postseason round reached | 416 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `team_payroll` | Inherited C04 franchise-season | Reported franchise player payroll | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Reported payroll and cap hit are not necessarily conceptually interchangeable. |
| `salary_cap_hit` | Inherited C04 franchise-season | Reported salary cap hit | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Reported payroll and cap hit are not necessarily conceptually interchangeable. |
| `salary_cap_space` | Inherited C04 franchise-season | Reported remaining salary cap room | 90 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `made_playoffs` | Inherited C04 franchise-season | Whether franchise qualified for playoffs | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `s18_tax_threshold` | Inherited C04 franchise-season | Luxury-tax threshold reported in S18 tax source | 688 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `taxable_payroll` | Inherited C04 franchise-season | Payroll measured for luxury-tax purposes | 450 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `tax_space` | Inherited C04 franchise-season | Difference between taxable payroll and reported tax threshold under source convention | 450 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `tax_paid` | Inherited C04 franchise-season | Reported realized luxury-tax payment | 268 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Actual tax payments and estimated costs are not interchangeable. |
| `estimated_tax_bill` | Inherited C04 franchise-season | Source-reported estimated luxury-tax bill | 94 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Actual tax payments and estimated costs are not interchangeable. |
| `total_estimated_cost` | Inherited C04 franchise-season | Source-defined estimated total payroll/tax cost | 450 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. Actual tax payments and estimated costs are not interchangeable. |
| `active_salary` | Inherited C04 franchise-season | Reported active-player salary component | 450 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `dead_salary` | Inherited C04 franchise-season | Reported inactive/dead salary component | 416 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `s18_source_file` | Inherited C04 franchise-season | Source filename for S18 tax/payroll data | 450 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `has_luxury_tax_source` | Inherited C04 franchise-season | Flag indicating S18 tax source availability | 776 | Inherited canonical source coverage; missingness and historical definitions vary by source and season. |
| `season_year` | Joined C01 league-season rules | Numeric season year used for joins/order | 776 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `salary_cap` | Joined C01 league-season rules | League salary-cap threshold for season | 776 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `salary_cap_floor_pct` | Joined C01 league-season rules | Salary floor as share of salary cap | 776 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `salary_cap_floor` | Joined C01 league-season rules | League minimum team salary floor | 776 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `league_luxury_tax_threshold` | Joined C01 league-season rules | League-wide luxury-tax threshold | 688 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `league_first_apron` | Joined C01 league-season rules | League first-apron threshold | 450 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `league_second_apron` | Joined C01 league-season rules | League second-apron threshold | 90 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `cba_id` | Joined C01 league-season rules | Collective bargaining agreement identifier | 776 | League thresholds and CBA regimes vary historically; missing or inapplicable values must not be interpreted as zero. |
| `total_apron_allocations` | Joined C10 apron status | Total franchise salary allocations used for apron comparisons | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `first_apron_threshold` | Joined C10 apron status | First-apron threshold used for franchise-season | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `second_apron_threshold` | Joined C10 apron status | Second-apron threshold used for franchise-season | 90 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `first_apron_space` | Joined C10 apron status | Headroom relative to first-apron threshold | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `second_apron_space` | Joined C10 apron status | Headroom relative to second-apron threshold | 90 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `above_first_apron` | Joined C10 apron status | Indicator for allocations exceeding first apron | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `above_second_apron` | Joined C10 apron status | Indicator for allocations exceeding second apron | 90 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `first_apron_hard_capped` | Joined C10 apron status | First-apron hard-cap status as recorded in C10 | 188 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `second_apron_hard_capped` | Joined C10 apron status | Second-apron hard-cap status as recorded in C10 | 90 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `second_apron_applicable` | Joined C10 apron status | Whether second-apron regime is applicable in season | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `apron_verification_status` | Joined C10 apron status | C10 apron verification status | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |
| `apron_source_coverage` | Joined C10 apron status | C10 apron source coverage indicator or category | 450 | Apron definitions, verification, and applicability vary by season; historical descriptive status does not imply modern CBA restrictions. |

## Documentation reconciliation and outstanding verification

- **Completed:** All 137 engineered feature names match the finalized CSV; the 73 remaining persisted fields are itemized above; non-null counts have been updated from the final CSV.
- **Completed as documentation:** Source dependency, descriptive definition, calculation logic, and limitations fields are populated for all 210 entries in the CSV. Some inherited-field descriptions are concise canonical summaries, not independent source-by-source revalidations.
- **Not claimed here:** A fresh-kernel, top-to-bottom rebuild and exact comparison against the exported Parquet. Earlier notebook validation is described above, but this documentation reconciliation does not independently repeat that execution.
- **Notebook comment follow-up:** v2A Group D implementation adds 26 features; a notebook comment reportedly says 27. Correct that comment when the notebook is next edited; the documented inventory uses 26.
- **Repository follow-up:** Update README links and source methodology; review Git status and commit/push after all documentation is finalized.

**Provenance:** This document reconciles the supplied A01 feature-library Markdown with the finalized `docs/a01_feature_library.csv` (210 fields, 776 franchise-seasons). Exact executable transformations remain in `notebooks/a01_franchise_season_build.ipynb`. No new feature engineering or notebook execution was performed during this documentation update.
