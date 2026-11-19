# A02 — Player–Franchise–Season Feature Library

Version: v1 — notebook- and CSV-reconciled documentation, 2026-11-18

Status: 183 persisted columns inventoried (62 inherited/joined or base fields; 121 F1–F5 analytical fields). Field coverage is taken from the finalized A02 CSV. Calculations and methodological choices are grounded in the supplied build notebook; this documentation exercise did not execute a fresh rebuild.

## Dataset and lineage

- **Grain:** one row per `(player_id, franchise_id, season)`. A player may have more than one franchise row in a season; this is not a unique player-season dataset.
- **Scope:** 12,810 player–franchise–season observations, 183 columns, 26 NBA seasons (2000–01 through 2025–26).
- **Canonical inputs:** C05 player-team-seasons (49 inherited fields), C03 players (identity/draft), C07 player salaries (matched at player–franchise–season), C01 seasons (financial thresholds/CBA), and C08 player awards (aggregated to player–season).
- **Analytical input:** A01 franchise-season Parquet for 15 franchise-context fields. The notebook also examines Spotrac-derived A01 active salary as a **comparison**, not a substitute for player salary.
- **Construction:** `notebooks/a02_player_franchise_season.ipynb`; final DuckDB view `a02_f5_ready`; exported `data/analytical/a02_player_franchise_season.parquet` with ZSTD compression.
- **Rebuild:** execute the notebook in order with canonical CSV inputs and A01 Parquet available. DuckDB TEMP VIEWs are session-scoped; the Parquet is a snapshot of the final view.
- **Coverage authority:** `docs/a02_feature_library.csv` reports stored types, non-null counts, coverage percentages, definitions, calculation summaries, source dependencies, and caveats. Coverage in the dictionary below is **non-null rows out of 12,810**, not nonzero or TRUE values.

## Feature-stage summary

| Stage | New analytical columns | Cumulative analytical columns |
|---|---:|---:|
| F1 — Compensation benchmarks | 12 | 12 |
| F1 — Salary allocation | 7 | 19 |
| F1 — Salary coverage | 2 | 21 |
| F1 — Cross-source salary reconciliation | 4 | 25 |
| F2 — Player production and contribution | 19 | 44 |
| F3 — Compensation vs. production | 9 | 53 |
| F4 — Awards and recognition | 26 | 79 |
| F4 — A01 franchise context | 15 | 94 |
| F5 — Temporal and longitudinal | 26 | 120 |
| F5 — Historical observation coverage | 1 | 121 |

**Inventory accounting:** 49 direct C05 performance fields + 13 joined/base identity, salary and CBA fields + 121 F1–F5 analytical fields = **183 persisted columns**. The 121 analytical fields include award and A01 context joins, not only newly calculated expressions. This distinction avoids incorrectly labeling every F4 field as independently engineered.

## Analytical feature dictionary

Formulas below summarize the supplied CSV and notebook. For entries whose CSV calculation is a notebook reference, the **notebook SQL is authoritative**; the reference is retained instead of inventing an expression. SQL NULL handling, ranking partitions, and tie behavior are defined by that code.

### F1 — Compensation benchmarks

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 1 | `salary_to_cap_ratio` | Player salary / season salary cap | Player salary / season salary cap | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 2 | `salary_rank_franchise_season` | Salary rank franchise season (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 3 | `salary_rank_league_season` | Salary rank league season (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 4 | `salary_percentile_league_season` | Salary percentile league season (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 5 | `league_mean_salary` | League mean salary (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 6 | `league_median_salary` | League median salary (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 7 | `team_mean_salary` | Team mean salary (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 8 | `team_median_salary` | Team median salary (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 9 | `salary_vs_league_mean` | Salary vs league mean (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 10 | `salary_vs_league_median` | Salary vs league median (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 11 | `salary_vs_team_mean` | Salary vs team mean (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 12 | `salary_vs_team_median` | Salary vs team median (see canonical/source semantics) | Defined in A02 notebook cell 6 (SQL expression); see executable SQL | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |

### F1 — Salary allocation

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 13 | `matched_roster_salary_total` | Matched roster salary total (see canonical/source semantics) | Defined in A02 notebook cell 8 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 14 | `matched_league_salary_total` | Matched league salary total (see canonical/source semantics) | Defined in A02 notebook cell 8 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 15 | `matched_roster_salary_count` | Matched roster salary count (see canonical/source semantics) | Defined in A02 notebook cell 8 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 16 | `recorded_roster_player_count` | Recorded roster player count (see canonical/source semantics) | Defined in A02 notebook cell 8 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 17 | `salary_share_recorded_roster` | Player salary / total matched salaries in recorded franchise-season | Player salary / total matched salaries in recorded franchise-season | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 18 | `salary_share_league` | Player salary / total matched league salaries for the season | Player salary / total matched league salaries for the season | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 19 | `roster_salary_match_rate` | Count of matched salaries / recorded roster-player count in franchise-season | Count of matched salaries / recorded roster-player count in franchise-season | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |

### F1 — Salary coverage

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 20 | `matched_league_salary_count` | Matched league salary count (see canonical/source semantics) | Defined in A02 notebook cell 11 (SQL expression); see executable SQL | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| 21 | `league_salary_match_rate` | Matched salary count / player observations in league-season | Matched salary count / player observations in league-season | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |

### F1 — Cross-source salary reconciliation

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 22 | `spotrac_active_salary` | Spotrac active salary (see canonical/source semantics) | Defined in A02 notebook cell 16 (SQL expression); see executable SQL | 7,889 | ESPN and Spotrac definitions/snapshots differ; comparison is exploratory, not reconciliation of equal accounting bases. |
| 23 | `salary_to_spotrac_active_ratio` | ESPN player salary / Spotrac active-salary total for franchise-season | ESPN player salary / Spotrac active-salary total for franchise-season | 6,681 | ESPN and Spotrac definitions/snapshots differ; comparison is exploratory, not reconciliation of equal accounting bases. |
| 24 | `espn_to_spotrac_roster_ratio` | Sum of matched ESPN roster salaries / Spotrac active-salary total | Sum of matched ESPN roster salaries / Spotrac active-salary total | 7,889 | ESPN and Spotrac definitions/snapshots differ; comparison is exploratory, not reconciliation of equal accounting bases. |
| 25 | `espn_spotrac_relative_gap` | Absolute ESPN roster-vs-Spotrac active salary difference / Spotrac active salary | Absolute ESPN roster-vs-Spotrac active salary difference / Spotrac active salary | 7,889 | ESPN and Spotrac definitions/snapshots differ; comparison is exploratory, not reconciliation of equal accounting bases. |

### F2 — Player production and contribution

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 26 | `minutes_total_est` | Games played × minutes per game (estimated due to source rounding) | Games played × minutes per game (estimated due to source rounding) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 27 | `blocks_total_est` | Games played × blocks per game (estimated) | Games played × blocks per game (estimated) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 28 | `steals_total_est` | Games played × steals per game (estimated) | Games played × steals per game (estimated) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 29 | `recorded_team_points_total` | Sum of recorded C05 player points for franchise-season | SUM over (franchise_id, season) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 30 | `recorded_team_assists_total` | Sum of recorded C05 player assists for franchise-season | SUM over (franchise_id, season) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 31 | `recorded_team_rebounds_total` | Sum of recorded C05 player rebounds for franchise-season | SUM over (franchise_id, season) | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 32 | `recorded_team_blocks_total_est` | Recorded team blocks total est (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 33 | `recorded_team_steals_total_est` | Recorded team steals total est (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 34 | `recorded_team_minutes_total_est` | Recorded team minutes total est (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 35 | `team_points_rank` | Team points rank (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 36 | `team_usage_rank` | Team usage rank (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 37 | `league_points_percentile` | League points percentile (see canonical/source semantics) | Defined in A02 notebook cell 18 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 38 | `team_scoring_share` | Player share of recorded franchise-season scoring | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 39 | `team_assist_share` | Player share of recorded franchise-season assist | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 40 | `team_rebound_share` | Player share of recorded franchise-season rebound | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 41 | `team_blocks_share` | Player share of recorded franchise-season blocks | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 42 | `team_steals_share` | Player share of recorded franchise-season steals | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 43 | `team_minutes_share` | Player share of recorded franchise-season minutes | Player measure / recorded franchise-season total | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 44 | `points_per_turnover` | Points per game / turnovers per game; null if denominator zero | Points per game / turnovers per game; null if denominator zero | 12,449 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |

### F3 — Compensation vs. production

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 45 | `team_minutes_rank` | Team minutes rank (see canonical/source semantics) | Defined in A02 notebook cell 22 (SQL expression); see executable SQL | 12,810 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 46 | `salary_scoring_share_gap` | Matched-roster salary share − recorded team scoring share | Matched-roster salary share − recorded team scoring share | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 47 | `salary_assist_share_gap` | Matched-roster salary share − recorded team assist share | Matched-roster salary share − recorded team assist share | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 48 | `salary_rebound_share_gap` | Matched-roster salary share − recorded team rebound share | Matched-roster salary share − recorded team rebound share | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 49 | `salary_scoring_rank_gap` | Franchise salary rank − franchise scoring rank | Franchise salary rank − franchise scoring rank | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 50 | `salary_to_scoring_share_ratio` | Matched-roster salary share / recorded scoring share | Matched-roster salary share / recorded scoring share | 10,558 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 51 | `salary_to_minutes_share_ratio` | Matched-roster salary share / recorded minutes share | Matched-roster salary share / recorded minutes share | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 52 | `salary_minutes_rank_gap` | Franchise salary rank − franchise minutes rank | Franchise salary rank − franchise minutes rank | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |
| 53 | `salary_usage_rank_gap` | Franchise salary rank − franchise usage rank | Franchise salary rank − franchise usage rank | 10,601 | Recorded C05 roster totals are not verified complete traded-player stints; estimated totals use rounded per-game inputs. |

### F4 — Awards and recognition

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 54 | `mvp_vote_rank` | Mvp vote rank (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 352 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 55 | `dpoy_vote_rank` | Dpoy vote rank (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 426 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 56 | `roy_vote_rank` | Roy vote rank (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 198 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 57 | `mvp_vote_share` | Mvp vote share (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 352 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 58 | `dpoy_vote_share` | Dpoy vote share (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 426 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 59 | `roy_vote_share` | Roy vote share (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 198 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 60 | `all_nba_team_selection` | All nba team selection (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 390 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 61 | `all_defensive_team_selection` | All defensive team selection (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 263 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 62 | `all_rookie_team_selection` | All rookie team selection (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 260 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 63 | `all_star_appearances` | All star appearances (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 681 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 64 | `award_type_count` | Sum of seven award win/selection indicators, excluding voting participation alone | Sum of seven award win/selection indicators, excluding voting participation alone | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 65 | `is_all_star` | Is all star (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 66 | `is_all_nba` | Is all nba (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 67 | `is_all_defensive` | Is all defensive (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 68 | `is_all_rookie` | Is all rookie (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 69 | `is_mvp` | Is mvp (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 70 | `is_dpoy` | Is dpoy (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 71 | `is_roy` | Is roy (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 72 | `mvp_top_3` | Mvp top 3 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 73 | `mvp_top_5` | Mvp top 5 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 74 | `dpoy_top_3` | Dpoy top 3 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 75 | `dpoy_top_5` | Dpoy top 5 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 76 | `roy_top_3` | Roy top 3 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 77 | `roy_top_5` | Roy top 5 (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 78 | `is_all_star_starter` | Is all star starter (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |
| 79 | `is_all_star_replacement` | Is all star replacement (see canonical/source semantics) | Defined in A02 notebook cell 29 (SQL expression); see executable SQL | 12,810 | Awards are aggregated to player-season; missing voting detail differs from non-selection; absence normalized to 0 for indicators. |

### F4 — A01 franchise context

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 80 | `team_win_pct` | Team win pct (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 81 | `team_made_playoffs` | Team made playoffs (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 82 | `team_round_score` | Team round score (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 6,760 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 83 | `team_win_pct_yoy_change` | Current value minus prior consecutive-season value for team win pct | Current − previous consecutive-season value | 12,337 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 84 | `context_team_payroll` | Context team payroll (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 85 | `team_payroll_to_cap_ratio` | Team payroll to cap ratio (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 86 | `team_payroll_percentile` | Team payroll percentile (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 87 | `team_payroll_yoy_growth` | Relative year-over-year change in team payroll | (Current − prior) / prior when prior > 0 | 12,337 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 88 | `team_above_tax_threshold` | Team above tax threshold (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 7,889 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 89 | `team_taxable_payroll_to_tax_ratio` | Team taxable payroll to tax ratio (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 7,889 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 90 | `team_tax_paid` | Team tax paid (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 4,040 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 91 | `team_payroll_normalized_efficiency` | Team payroll normalized efficiency (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 12,810 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 92 | `team_avg_win_pct_3yr` | Team avg win pct 3yr (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 11,867 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 93 | `team_avg_payroll_to_cap_3yr` | Team avg payroll to cap 3yr (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 11,867 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |
| 94 | `team_playoff_appearances_3yr` | Team playoff appearances 3yr (see canonical/source semantics) | Defined in A02 notebook cell 38 (SQL expression); see executable SQL | 11,867 | Franchise context repeated for each player row; A01 tax/apron status is descriptive proxy, not verified official status. |

### F5 — Temporal and longitudinal

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 95 | `season_start_year` | First four characters of season label cast to integer | First four characters of season label cast to integer | 12,810 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 96 | `points_per_36` | Points per game × 36 / minutes per game, where minutes > 0 | Points per game × 36 / minutes per game, where minutes > 0 | 12,810 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 97 | `has_previous_season` | True when previous observed player season is immediately adjacent | True when previous observed player season is immediately adjacent | 10,226 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 98 | `changed_franchise` | Franchise differs from previous consecutive observed player season; otherwise unknown for gaps | Franchise differs from previous consecutive observed player season; otherwise unknown for gaps | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 99 | `consecutive_seasons_with_franchise` | Row number within uninterrupted franchise stint, restarting after gaps or moves | Row number within uninterrupted franchise stint, restarting after gaps or moves | 12,810 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 100 | `seasons_since_first_observed` | Current start year − first observed player start year (not full career tenure) | Current start year − first observed player start year (not full career tenure) | 12,810 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 101 | `prev_ppg` | Prior consecutive-season value of ppg | LAG by player, returned only when previous observed season is adjacent | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 102 | `prev_minutes_per_game` | Prior consecutive-season value of minutes per game | LAG by player, returned only when previous observed season is adjacent | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 103 | `prev_points_per_36` | Prior consecutive-season value of points per 36 | LAG by player, returned only when previous observed season is adjacent | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 104 | `prev_salary` | Prior consecutive-season value of salary | LAG by player, returned only when previous observed season is adjacent | 8,620 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 105 | `prev_salary_share` | Prior consecutive-season value of salary share | LAG by player, returned only when previous observed season is adjacent | 8,620 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 106 | `prev_salary_to_cap_ratio` | Prior consecutive-season value of salary to cap ratio | LAG by player, returned only when previous observed season is adjacent | 8,620 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 107 | `prev_salary_scoring_share_gap` | Prior consecutive-season value of salary scoring share gap | LAG by player, returned only when previous observed season is adjacent | 8,620 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 108 | `ppg_yoy_change` | Current points per game − previous consecutive-season points per game | Current points per game − previous consecutive-season points per game | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 109 | `ppg_yoy_growth` | (Current PPG − previous PPG) / previous PPG, if previous > 0 | (Current PPG − previous PPG) / previous PPG, if previous > 0 | 9,813 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 110 | `minutes_yoy_change` | Current value minus prior consecutive-season value for minutes | Current − previous consecutive-season value | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 111 | `points_per_36_yoy_change` | Current value minus prior consecutive-season value for points per 36 | Current − previous consecutive-season value | 9,850 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 112 | `salary_yoy_change` | Current value minus prior consecutive-season value for salary | Current − previous consecutive-season value | 8,126 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 113 | `salary_yoy_growth` | Relative year-over-year change in salary | (Current − prior) / prior when prior > 0 | 8,126 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 114 | `salary_share_yoy_change` | Current value minus prior consecutive-season value for salary share | Current − previous consecutive-season value | 8,126 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 115 | `salary_to_cap_yoy_change` | Current value minus prior consecutive-season value for salary to cap | Current − previous consecutive-season value | 8,126 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 116 | `value_gap_yoy_change` | Current value minus prior consecutive-season value for value gap | Current − previous consecutive-season value | 8,126 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 117 | `avg_ppg_prior_3yr` | Average points per game across available prior three calendar seasons (excluding current) | Average points per game across available prior three calendar seasons (excluding current) | 10,179 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 118 | `avg_minutes_prior_3yr` | Average minutes per game across available prior three calendar seasons | Average minutes per game across available prior three calendar seasons | 10,179 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 119 | `avg_salary_share_prior_3yr` | Average matched-roster salary share across available prior three calendar seasons | Average matched-roster salary share across available prior three calendar seasons | 9,266 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |
| 120 | `ppg_vs_prior_3yr` | Current points per game − average of available prior 3 calendar-season PPG values | Current points per game − average of available prior 3 calendar-season PPG values | 10,179 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |

### F5 — Historical observation coverage

| # | Feature | Definition | Calculation summary | Non-null | Caveat |
|---:|---|---|---|---:|---|
| 121 | `prior_3yr_seasons_observed` | Count of observed player seasons in preceding three calendar seasons | Count of observed player seasons in preceding three calendar seasons | 12,810 | Observed player history begins 2000–01; consecutive-year flags require adjacent observations; prior-three-year averages may use 1–3 observed seasons. |

## Design choices, deferrals, and interpretive limits

1. **Preserve C05's grain and rows.** A02 starts from the complete canonical player–franchise–season performance table and LEFT JOINs supporting sources. It does not drop a player observation when salary, identity metadata, awards, or team context is missing. This protects production analysis from salary-source selection bias, while leaving compensation analyses on a smaller matched sample.
2. **Salary matches are incomplete and explicit.** The notebook's base validation records **10,601 salary-matched rows and 2,209 unmatched rows** out of 12,810. `salary_available` marks observed salary availability; missing salary is not a zero salary. Salary ranks, ratios and value comparisons should be analyzed conditional on coverage.
3. **Recorded salary pools are not official payroll totals.** F1 computes matched roster and league salary sums and counts from C07 matches. `salary_share_recorded_roster` uses the **matched** roster denominator. `roster_salary_match_rate` and `league_salary_match_rate` make denominator completeness visible. Do not treat these sums as audited franchise payroll or as necessarily equal to A01 payroll.
4. **Cross-source salary comparison is a diagnostic.** The ESPN/C07-derived matched roster salary total is compared against the Spotrac active-salary field brought from A01. These sources have different accounting definitions and possible coverage differences. The resulting ratios and gaps flag reconciliation questions; they are not definitive proof that either source is incorrect.
5. **Player production is not complete team production.** F2 aggregates recorded C05 player totals by franchise-season and creates contribution shares against those recorded sums. These denominators can differ from official team totals, particularly when source coverage, trades, or rounding differ. Minutes, blocks and steals totals derived from per-game rates are explicitly estimates.
6. **Compensation-versus-production features are descriptive proxies.** F3 compares salary shares/ranks with scoring, minutes, usage, assists and rebounds. It does not estimate marginal wins, causal contribution, or contract surplus value. A positive or negative gap is a signal for investigation, not a standalone player-value verdict.
7. **Award records require semantic care.** C08 is aggregated at player-season level before joining to A02. Selection/win indicators, vote ranks, vote shares and top-three/top-five flags are different concepts. Missing vote data should not be interpreted as proof of no voting participation. Award flags normalized after the LEFT JOIN are documented as recorded-selection indicators.
8. **Player-season awards may repeat across franchise rows.** A player with multiple franchises in the same season receives the player-season award context on each franchise row. Aggregating awards across A02 rows without de-duplication can double-count player-season recognition.
9. **A01 context is contextual, not player-specific.** The 15 joined A01 fields describe the player's franchise-season, not the individual player's causal effect on team performance. A01 financial threshold and tax caveats carry forward, including distinctions between observed tax payments, source estimates and historical CBA applicability.
10. **Temporal continuity is enforced for prior-season comparisons.** F5 builds a season start year, detects previous observed seasons and franchise changes, and gates LAG-derived prior values and year-over-year deltas on adjacent calendar seasons. A gap in the source history is not assumed to represent an unchanged value or a zero.
11. **Multiple-franchise player-seasons require an explicit ordering policy.** The notebook inspects multi-franchise seasons and defines its player-level temporal ordering before lag calculations. These fields describe the notebook's chosen observational sequence, not a complete transaction history; verify the ordering logic before interpreting franchise-switch effects causally.
12. **Historical windows differ from A01 rolling windows.** A02's `avg_*_prior_3yr` features exclude the current season and average available prior three calendar-season observations; `prior_3yr_seasons_observed` records how many are present. They are not guaranteed three-observation averages. By contrast, A01's 3/6-year rolling features require complete consecutive windows and include the current season.
13. **Deferred extensions.** The present feature library does not construct true contract-level surplus value, injury-adjusted value, transaction-level movement timing, official team-payroll allocation, or causal player impact. Those would require additional verified source data or an explicit modeling design. These are proposed extensions, not claims that the notebook previously evaluated and rejected each one.
14. **Upstream provenance belongs in the broader methodology.** This document identifies where canonical source coverage affects A02. Detailed raw-source extraction, staging transformations, entity resolution and unresolved salary reconciliation belong in `docs/source_methodology.md` and canonical audit materials.
15. **Export storage is deliberate.** The notebook casts five integer-valued aggregate totals to BIGINT on export (`matched_roster_salary_total`, `matched_league_salary_total`, `recorded_team_points_total`, `recorded_team_assists_total`, `recorded_team_rebounds_total`) and checks exported types and values. A type change at export does not imply a feature-definition change.

## Inherited and joined field inventory (62 columns)

These are the 49 fields inherited from C05 and the 13 identity/compensation/CBA fields added to the base before F1–F5. Some joined base fields are calculated flags (`salary_available`); the grouping here is by **pipeline stage**, not by whether a field uses any SQL expression. Definitions are concise canonical/source summaries and should not be read as a fresh source-by-source audit.

| Field | Origin | Definition | Non-null | Caveat |
|---|---|---|---:|---|
| `player_id` | Inherited C05 player performance / C05 | Player id (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `franchise_id` | Inherited C05 player performance / C05 | Franchise id (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `season` | Inherited C05 player performance / C05 | Season (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `age` | Inherited C05 player performance / C05 | Age (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `height_inches` | Inherited C05 player performance / C05 | Height inches (see canonical/source semantics) | 12,741 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `weight_lbs` | Inherited C05 player performance / C05 | Weight lbs (see canonical/source semantics) | 12,797 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `games_played` | Inherited C05 player performance / C05 | Games played (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `points_total` | Inherited C05 player performance / C05 | Points total (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `rebounds_total` | Inherited C05 player performance / C05 | Rebounds total (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `assists_total` | Inherited C05 player performance / C05 | Assists total (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `wins` | Inherited C05 player performance / C05 | Wins (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `losses` | Inherited C05 player performance / C05 | Losses (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `win_pct` | Inherited C05 player performance / C05 | Win pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `minutes_per_game` | Inherited C05 player performance / C05 | Minutes per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `field_goals_made_per_game` | Inherited C05 player performance / C05 | Field goals made per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `field_goals_attempted_per_game` | Inherited C05 player performance / C05 | Field goals attempted per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `field_goal_pct` | Inherited C05 player performance / C05 | Field goal pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `three_pointers_made_per_game` | Inherited C05 player performance / C05 | Three pointers made per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `three_pointers_attempted_per_game` | Inherited C05 player performance / C05 | Three pointers attempted per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `three_point_pct` | Inherited C05 player performance / C05 | Three point pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `free_throws_made_per_game` | Inherited C05 player performance / C05 | Free throws made per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `free_throws_attempted_per_game` | Inherited C05 player performance / C05 | Free throws attempted per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `free_throw_pct` | Inherited C05 player performance / C05 | Free throw pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `offensive_rebounds_per_game` | Inherited C05 player performance / C05 | Offensive rebounds per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `defensive_rebounds_per_game` | Inherited C05 player performance / C05 | Defensive rebounds per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `rebounds_per_game` | Inherited C05 player performance / C05 | Rebounds per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `assists_per_game` | Inherited C05 player performance / C05 | Assists per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `turnovers_per_game` | Inherited C05 player performance / C05 | Turnovers per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `steals_per_game` | Inherited C05 player performance / C05 | Steals per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `blocks_per_game` | Inherited C05 player performance / C05 | Blocks per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `personal_fouls_per_game` | Inherited C05 player performance / C05 | Personal fouls per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `points_per_game` | Inherited C05 player performance / C05 | Points per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `plus_minus_per_game` | Inherited C05 player performance / C05 | Plus minus per game (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `offensive_rating` | Inherited C05 player performance / C05 | Offensive rating (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `defensive_rating` | Inherited C05 player performance / C05 | Defensive rating (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `net_rating` | Inherited C05 player performance / C05 | Net rating (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `assist_pct` | Inherited C05 player performance / C05 | Assist pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `assist_to_turnover_ratio` | Inherited C05 player performance / C05 | Assist to turnover ratio (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `assist_ratio` | Inherited C05 player performance / C05 | Assist ratio (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `offensive_rebound_pct` | Inherited C05 player performance / C05 | Offensive rebound pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `defensive_rebound_pct` | Inherited C05 player performance / C05 | Defensive rebound pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `rebound_pct` | Inherited C05 player performance / C05 | Rebound pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `team_turnover_pct` | Inherited C05 player performance / C05 | Team turnover pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `effective_field_goal_pct` | Inherited C05 player performance / C05 | Effective field goal pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `true_shooting_pct` | Inherited C05 player performance / C05 | True shooting pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `usage_pct` | Inherited C05 player performance / C05 | Usage pct (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `pace` | Inherited C05 player performance / C05 | Pace (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `player_impact_estimate` | Inherited C05 player performance / C05 | Player impact estimate (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `possessions` | Inherited C05 player performance / C05 | Possessions (see canonical/source semantics) | 12,810 | Source coverage/definitions inherited from C05; see canonical methodology. |
| `player_name` | Base identity, compensation, CBA / C03 / C07 / C01 | Player name (see canonical/source semantics) | 12,810 | Display label; use player_id for joins because names may be non-unique or vary across sources. |
| `country` | Base identity, compensation, CBA / C03 / C07 / C01 | Country (see canonical/source semantics) | 12,810 | Biographical attribute; source conventions and missingness may differ; not a season-specific nationality measure. |
| `college` | Base identity, compensation, CBA / C03 / C07 / C01 | College (see canonical/source semantics) | 10,846 | Biographical attribute; may be absent for players without a recorded college; not evidence of missing player identity. |
| `draft_year` | Base identity, compensation, CBA / C03 / C07 / C01 | Draft year (see canonical/source semantics) | 12,810 | May be null for undrafted players or incomplete historical source records; distinguish these cases before analysis. |
| `draft_round` | Base identity, compensation, CBA / C03 / C07 / C01 | Draft round (see canonical/source semantics) | 12,719 | May be null for undrafted players or incomplete historical source records; historical draft formats differ. |
| `draft_number` | Base identity, compensation, CBA / C03 / C07 / C01 | Draft number (see canonical/source semantics) | 12,707 | May be null for undrafted players or incomplete historical source records; historical draft formats differ. |
| `salary` | Base identity, compensation, CBA / C03 / C07 / C01 | Salary (see canonical/source semantics) | 10,601 | Matched-salary coverage is incomplete; absent salary is not zero. |
| `salary_available` | Base identity, compensation, CBA / C03 / C07 / C01 | 1 if a matching C07 salary is present; otherwise 0 | 12,810 | Matched-salary coverage is incomplete; absent salary is not zero. |
| `salary_cap` | Base identity, compensation, CBA / C03 / C07 / C01 | Salary cap (see canonical/source semantics) | 12,810 | Season-level league rule repeated on player-franchise-season rows; do not aggregate across players. |
| `luxury_tax_threshold` | Base identity, compensation, CBA / C03 / C07 / C01 | Luxury tax threshold (see canonical/source semantics) | 11,465 | Season-level league rule repeated on player-franchise-season rows; historical tax definitions and availability may vary. |
| `first_apron_threshold` | Base identity, compensation, CBA / C03 / C07 / C01 | First apron threshold (see canonical/source semantics) | 7,889 | Season-level league rule repeated on player-franchise-season rows; apron rules are not applicable to all historical seasons. |
| `second_apron_threshold` | Base identity, compensation, CBA / C03 / C07 / C01 | Second apron threshold (see canonical/source semantics) | 1,723 | Season-level league rule repeated on player-franchise-season rows; apron rules are not applicable to all historical seasons. |
| `cba_id` | Base identity, compensation, CBA / C03 / C07 / C01 | Cba id (see canonical/source semantics) | 12,810 | League-rule identifier; use as a reference key, not a continuous numeric predictor or substantive measure. |

## Documentation reconciliation and outstanding verification

- **Completed as an inventory:** All 183 A02 CSV column names are represented exactly once in this document: 121 F1–F5 fields and 62 base/inherited fields. Field coverage values come directly from the uploaded finalized CSV.
- **Notebook-grounded:** Stage lineage, principal calculation choices, salary matching strategy, award aggregation, A01 context joins, longitudinal construction, and export checks are described from the uploaded A02 notebook.
- **Not claimed:** A fresh-kernel top-to-bottom rebuild, full numerical comparison of the notebook's final view with Parquet, or independent revalidation of every source-field semantic definition in this documentation pass. Earlier QA checks are present in the notebook.
- **Follow-up:** In `source_methodology.md`, cross-reference canonical source limitations, salary reconciliation, and A01-to-A02 joins. Then update README links and commit/push documentation together.

**Provenance:** Reconciled against `docs/a02_feature_library.csv` (183 rows) and the supplied 58-cell A02 construction/QA notebook. Structure follows the supplied reconciled A01 feature-library Markdown. The uploaded Parquet is the saved output reference; no source transformations were changed to produce this document.
