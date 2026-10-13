# Decisions and omissions

One entry per decision or simplification. Used later for the limitations section.
Format: what, why, likely effect, what I would do with more time.

## 2026-10-12

### Column names
- What: all 63 columns renamed by hand (lowercase with underscores, prefix `cur_` for the current employer, `prev_` for previous employers, suffix `_text` for free text).
- Why: the original names are full questions, too long for plots and the report.
- Likely effect: none on the results; readability only. The renaming exists only in the notebook.
- With more time: nothing.
- Originals: kept in `original_columns` and in `data/column_map.csv`.

### Structurally missing values
- What: `employer_is_tech`, `role_is_tech` and `mh_coverage_self_emp` are not imputed. Their gaps come from the survey's skip logic ("not asked"), not from non-response.
- Why: imputing would invent most of the answers (e.g. 1,170 of 1,433 values for `role_is_tech`).
- Likely effect: open; final handling (drop, merge or keep as subgroup descriptor) is decided in Phase 5.
- With more time: compare results with and without these columns.

### Raw data
- What: the raw CSV in `data/raw/` stays unchanged; all cleaning happens in the notebook.
- Why: reproducibility.

### Free-text answers
- What: free-text columns (37, 39, 48, 49, 51) and gender are inspected only with counts, not by printing individual answers.
- Why: health details and re-identification risk (Phase 1, #15).