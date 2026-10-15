# Decisions and omissions

One entry per decision or simplification. Used later for the limitations section.
Format: what, why, likely effect, what I would do with more time.
Phases 0-2 were done on 9-10 Oct 2026, Phase 3 and 4 on 12-15 Oct 2026.

## Phase 0: working environment

### Python version
- What: Python 3.14 (python.org); the installed 3.15 alpha is not used.
- Why: 3.15 was only a pre-release, and scikit-learn and matplotlib did not support it yet.
- Likely effect: all libraries install and run on a stable version.
- With more time: move to a newer Python once the libraries support it.

### No virtual environment
- What: packages are installed directly into Python 3.14; Python and pip are always called as `python3.14` and `python3.14 -m pip`.
- Why: direct installation keeps the setup simple; the explicit calls prevent mixing it up with the 3.15 alpha.
- Likely effect: packages are shared with other projects on this computer; the versions of the project's packages are saved in `requirements.txt`.
- With more time: use a virtual environment for a cleaner, reproducible setup.

### Editor and notebook
- What: VS Code on a Mac, with one analysis notebook (`analysis.ipynb`).
- Open: whether repeated code moves into a separate `utils.py`.

### Library workarounds
- What: `setuptools` is installed because yellowbrick still imports `distutils`. Fallback: build the elbow and silhouette plots with scikit-learn and matplotlib. pandas 3.0 is used (text columns show as `str`).
- Likely effect: tutorial code written for pandas 2.x may behave differently.

### GitHub repository
- What: the repository stays private until shortly before submission (step 61). `.gitignore` excludes `.ipynb_checkpoints/`, `__pycache__/` and `.DS_Store`.
- Why: the work stays private until it is checked.

## Phase 1: understanding the problem

### Task and dataset (#16)
- What: Task 1 (mental health in technology-related jobs) with the OSMI Mental Health in Tech Survey 2016 from Kaggle; only the CSV is used (1,433 rows, 63 columns).
- Why: the dataset is suggested in the task, and the CSV is the only file needed.

### Representativeness (#3)
- What: the survey is accepted as representative, as the task states. The limitations get at most one sentence on self-selection and the US-heavy sample (59% of respondents).
- Why: it is a premise of the scenario; arguing against it costs pages and looks like avoiding the task.
- Likely effect: the limitation is visible but does not dominate the report.

### Shared industry context (#2)
- What: I claim a "shared industry context", not "aligned values".
- Why: "technology-oriented" describes the field of work, not attitudes.

### Limits of workplace measures (#4)
- What: introduction and conclusion name the limits of a workplace program, because mental health has several causes outside the employer's control.
- Why: it protects against overpromising. The multi-cause claim needs a source (Phase 2, area A).

### Descriptive, not causal (#5)
- What: levers for HR are presented as hypotheses backed by literature, not as proven effects.
- Why: clusters show who is similar, not what causes what, and the survey is a one-time snapshot.

### Easy to understand and easy to implement (#7)
- What: few clusters, neutral plain names, labeled plots with a reading aid, explained variance stated; recommendations sorted by effort versus impact.
- Why: HR is non-technical, and the results must be usable.
- Likely effect: easy measures can be superficial, so structural long-term measures are marked as such.

### Two kinds of "concept" (#8)
- What: the technical concept goes into the methodology, the organizational concept for HR into the results or conclusion.
- Why: the task's "concept for implementation" can mean either.

### Iterations (#9)
- What: only the 2-3 iterations that changed something are documented; the rest stays in the notebook and the commit history. The HR program is described as pilot, evaluate, adjust.
- Why: the report has 7-10 pages.

### Order of methods (#11)
- What: explore, plan, preprocess, engineer and select features, reduce dimensions, cluster, evaluate, interpret, iterate.
- Why: the task's list misses planning, preprocessing, evaluation and interpretation.

### Missing values and number of clusters (#13)
- What: structural missingness is treated differently from non-response. The number of clusters k is justified with metrics and interpretability.
- Why: there is no ground truth, and the task does not give k.

### Ethics measures (#15)
- What: neutral cluster names, a minimum cluster size before reporting, aggregate outputs only, use for the program only; the gender-cleaning choice is documented.
- Why: health data is a special category under the GDPR, and small clusters can identify people.
- Likely effect: risks are weighed against usefulness; too much caution would make the results useless.

### Publishing the data (#17, #18)
- What: the unchanged CSV goes into `data/raw/` of the public repository with its original file name. The license (CC BY-SA 4.0) and OSMI are credited in the README. No task PDF and no task text in the repository or README.
- Why: the work stays reproducible and graders can run the code; IU holds the copyright to the task.

### Writing the report (#10, #19)
- What: the final report is written in my own words; numbers in the introduction need a citable source; omissions are logged in this file.
- Why: independent work is graded and the text is checked for plagiarism.

### Open from Phase 1
- Feature scope (#6): all questions, workplace questions only, or workplace questions for clustering with personal factors only to describe the clusters. Open until Phase 5; the justification goes into the report.
- Role of dimensionality reduction (#12): for visualization only, or also before clustering. Open until Phase 5 and 7.

## Phase 2: sources

### What can be cited
- What: peer-reviewed or primary sources in APA 7th edition. Course books, slides and webinars are not cited; Kaggle notebooks and blogs are inspiration only.
- Why: IU citation guidelines.

### Source set
- What: 12 core sources plus the GDPR text, covering the areas domain, framework, dataset, methods, missing data, ethics and interventions.
- Likely effect: several sources come from one research network (Harvey, Joyce, Gayed, partly LaMontagne), so the report gets one sentence on source independence.

### Method citations
- What: Rousseeuw (1987) for the silhouette score; Roweis and Saul (2000) for LLE instead of the course book's unpublished draft. Gower distance and k-modes (Gower 1971, Huang 1998) are an alternative to one-hot encoding plus k-means.
- Open: whether Gower distance or k-modes are used (Phase 6 and 8).

### Citations still to verify
- Schwarz (1978), MacQueen (1967), Ward (1963), Jolliffe, Pedregosa et al. (2011), and the German provisions (§ 87 (1) BetrVG, § 5 ArbSchG) are cited only if used and only after verification.

### Structure of the recommendations
- What: recommendations are sorted by prevention level (Joyce) or into protect, promote and respond (LaMontagne, optional). Manager training is presented as a hypothesis (WHO recommendation 4).

## Phase 3: data

### Raw data
- What: the raw CSV in `data/raw/` stays unchanged; all cleaning happens in the notebook.
- Why: reproducibility.

## Phase 4: exploratory data analysis

### Column names
- What: all 63 columns renamed by hand (lowercase with underscores, prefix `cur_` for the current employer, `prev_` for previous employers, suffix `_text` for free text, suffix `_list` for multi-select lists, suffix `_self_emp` for columns answered only by the self-employed).
- Columns 48, 49 and 51 were renamed from `..._conditions_text` to `..._conditions_list` (14 Oct), because step 27b showed they are multi-select lists, not free text. Only the two "why" columns end in `_text` now.
- Why: the original names are full questions, too long for plots and the report.
- Likely effect: none on the results; readability only. The renaming exists only in the notebook.
- Originals: kept in `original_columns` and in `data/column_map.csv`.
- Column 17 was renamed from `knows_help_resources` to `knows_help_resources_self_emp` (14 Oct), because a crosstab showed that only the self-employed answered it. Learning: check who was asked before naming a column.
- Columns 18-21 (`reveal_to_clients`, `reveal_clients_neg_impact`, `reveal_to_coworkers`, `reveal_coworkers_neg_impact`) get the same suffix `_self_emp` (decided 14 Oct, option A: consistent with columns 16 and 17, even though the names get long). Option B, keeping short names and marking it only in the codebook, was rejected because someone could then use the columns for everyone.
- Columns 22-23 (`productivity_affected`, `productivity_pct_affected`) get the suffix too (option A again). They were renamed on 14 Oct, together and after block 2 was classified, so the notebook was rebuilt only once for the pair.

### Structurally missing values
- What: `employer_is_tech`, `role_is_tech`, `mh_coverage_self_emp`, `knows_help_resources_self_emp`, the four `reveal_..._self_emp` columns and the two productivity columns are not imputed. Their gaps come from the survey's skip logic ("not asked"), not from non-response.
- Why: imputing would invent most of the answers (1,170 of 1,433 values for `role_is_tech`, 1,146 of 1,433 for each of the eight self-employed-only columns).
- Likely effect: open; final handling (drop, merge or keep as subgroup descriptor) is decided in Phase 5.
- With more time: compare results with and without these columns.
- `cur_knows_care_options` has 133 gaps beyond the 287 self-employed. 127 of them come from employed people without benefits (72) or not eligible (55); everyone with benefits answered. It is not a clean skip rule, so the 133 count as non-response linked to an observed answer (checked 14 Oct).

### Columns answered by a subgroup only
- What: eight columns (16 to 23: `mh_coverage_self_emp`, `knows_help_resources_self_emp`, the four `reveal_..._self_emp` columns and the two productivity columns) describe only the 287 self-employed; `role_is_tech` only the 263 employed people at non-tech employers. Until Phase 5 decides, they are not used as features for all respondents.
- Why: for most respondents these columns contain no information.
- Likely effect: access to help is measured by columns 4, 5 and 7 for the employed and by columns 16 and 17 for the self-employed. Openness about mental health is measured by the `cur_comfort_` and `cur_..._conseq` columns for the employed and by columns 18-21 for the self-employed; whether the two sets are comparable is not checked.
- With more time: analyse the self-employed subgroup separately (small: 287).
- Open: with eight columns answered only by the self-employed, the feature scope (#6) decides whether they are dropped or form a separate analysis of the 287 self-employed (Phase 5).

### Free-text answers
- What: the free-text columns (37, 39), the condition lists (48, 49, 51) and gender are inspected only with counts, not by printing individual answers or item names.
- Why: health details and re-identification risk (#15).

### Scale levels (step 25)
- What: scale levels are assigned per column by judgment and stored in the `scale_levels` dictionary (copied into `column_map` in step 25b on 14 Oct). Rules so far: Yes / No / "I don't know" answers are nominal; Yes / Maybe / No answers are ordinal; company size bins are ordinal; two-value columns are binary; answer sets that mix different kinds of "No" or contain "Not applicable" or "I'm not sure" are nominal.
- Why: the survey does not state a scale level, and the encoding (Phase 6) and the distance measure (Phase 7 and 8) depend on it.
- Likely effect: an ordinal reading keeps more information but assumes an order; a nominal reading is safer but ignores the order.
- With more time: run the clustering with both readings for the debatable columns and compare.
- Status: all 63 columns classified (14 Oct).

### Maybe as middle category
- What: "Maybe" is treated as the middle between No and Yes in `cur_mh_disclosure_conseq`, `cur_ph_disclosure_conseq`, `cur_comfort_coworkers` and `cur_comfort_supervisor`.
- Why: these columns have no "I don't know" option, so Maybe plausibly marks a middle position.
- Likely effect: Maybe can also mean "uncertain"; as ordinal, those respondents are placed between No and Yes. Distances between steps are not assumed equal.
- With more time: test the nominal reading.

### Company size
- What: `company_size` stays ordinal and is not converted to numbers.
- Why: the bins are unequal in width, the top bin has no upper limit, and the labels overlap at 100 and 500; any midpoint would be an invented number.
- Likely effect: bin order is kept, differences between bins are not used.

### "I don't know" answers
- What: "I don't know" and "I am not sure" are not placed on an ordinal scale. `cur_leave_ease` is ordinal for its five steps, with "I don't know" (150 answers) as the exception. `cur_knows_care_options` is nominal; an ordinal reading (No, Not sure, Yes) is possible.
- Why: "I don't know" is not a midpoint, and it is informative itself (742 of 1,146 answers to `cur_anonymity_protected`).
- Likely effect: treating it as missing would discard many answers.
- Open: one rule for all columns (own category, indicator column or missing) is decided in Phase 6.

### Direction of "Yes"
- What: "Yes" is the bad answer in the `_conseq` columns and the good answer in the `_comfort_` columns. The direction is aligned before answers are coded as numbers.
- Why: otherwise a distance measure treats "Yes" as the same thing in both.
- The same holds for the interview columns ("Yes" is the open answer) against `career_hurt_if_identified` and `coworkers_view_negatively` ("Yes" is the bad answer), and for the `prev_` columns.
- With more time: check the other blocks for the same issue.

### Reveal columns (columns 18-21)
- What: the four reveal columns (reveal to clients, reveal to coworkers, and the two negative-impact follow-ups) were asked only of the 287 self-employed. All four are nominal. `reveal_clients_neg_impact_self_emp` has 143 missing values even among the self-employed, because it is a conditional follow-up.
- Why nominal: the answers mix how often someone reveals with two different reasons for "No" ("it doesn't matter" and "it would impact me negatively"), plus "Not applicable to me" and "I'm not sure", which are not midpoints.
- Likely effect: the impact columns measure perceived, not necessarily experienced, impact: 24 people who do not reveal because it would hurt them still answered "Yes" to the impact question.
- Correction: earlier notes described these columns as conditional on a diagnosis; the gate is the self-employed split.
- With more time: analyse the self-employed subgroup separately.

### Productivity columns (columns 22-23)
- What: `productivity_affected` was asked only of the 287 self-employed (nominal). `productivity_pct_affected` was asked only of the 204 who answered "Yes" (ordinal: 1-25%, 26-50%, 51-75%, 76-100%).
- Why: "Unsure" and "Not applicable to me" are not midpoints; the percentage answers are ranges, and replacing them with midpoints would invent precision.
- Likely effect: the 83 self-employed respondents who did not say "Yes" were not asked the percentage question (structural missing values inside the subgroup). The 71% "Yes" describes the self-employed who answered; it is not compared with the employed, who were not asked.
- Both columns carry the suffix `_self_emp` (renamed 14 Oct).

### Previous-employer block (columns 24-35)
- What: `has_prev_employers` is binary. The 11 `prev_` columns were asked only of the 1,264 respondents with previous employers; the 169 without answered none of them, with no partial answers. They are not imputed.
- Why: the gate holds without exception, so the gaps are "not asked".
- Likely effect: the block describes 88% of respondents. How the 169 enter the clustering (drop the block, own category, or the 1,264 only) is open until Phase 5 and 6.

### Different answer scales for previous employers
- What: previous-employer questions use None / Some / All, current-employer questions use Yes / No / "I don't know". Their scale levels are assigned separately, and columns are not lined up by name when the two blocks are compared.
- Why: the options differ, so "Yes" for the current employer is not "Yes, they all did".
- Likely effect: redundancy between the two blocks (Phase 6) must be checked on a common coding, not by column name.

### Previous-employer scale levels
- What: ordinal (None < Some < All): `prev_benefits_provided`, `prev_employer_discussed_mh`, `prev_resources_offered`, `prev_mh_disclosure_conseq`, `prev_ph_disclosure_conseq`, `prev_willing_coworkers`, `prev_willing_supervisor`, `prev_mh_equals_ph`, `prev_observed_neg_conseq`. Nominal: `prev_knew_care_options` (mixes how much and when people knew; "N/A (not currently aware)" is ambiguous) and `prev_anonymity_protected` (68% "I don't know").
- Why: the ordinal columns have a clear order of extent. Where "I don't know" is a small group (7-26%) it is an exception to the order; where it is the majority (68%) the column is nominal.
- Likely effect: the line between 26% and 68% is a judgment call, not a rule, and "Some" covers one employer up to nearly all.
- With more time: run both readings for the borderline columns (`prev_benefits_provided`, `prev_mh_equals_ph`, `prev_anonymity_protected`).

### Observed negative consequences
- What: the observed-negative-consequences columns of the two blocks are not compared as rates (40% for previous employers, 9% for the current employer).
- Why: the previous-employer question covers several employers over a career, so the chance of observing a case is higher by construction.

### Interview and career attitudes (columns 36-41)
- What: the interview, career and coworker columns are ordinal. `career_hurt_if_identified` and `coworkers_view_negatively` have five steps, with the answers that report experience ("Yes, it has", "No, it has not") placed at the ends.
- Why: the answers mix expectation and experience along one direction (would it hurt).
- Likely effect: the placement of the experience answers is an assumption. Alternatives: nominal, or three levels (No, Maybe, Yes), which drops the information about the 135 and 91 people who report real experience.
- With more time: run the clustering with the three-level version.

### Unsupportive response and its follow-up (columns 43-44)
- What: `observed_unsupportive_response` is nominal ("Yes, I observed" and "Yes, I experienced" are different events, not degrees). Its 89 missing values follow no skip rule and are treated as probable non-response. `observation_less_likely_reveal` is ordinal; its 776 missing values are 656 structural (not asked of people who answered "No" or gave no answer) and 120 real non-response.
- Why: crosstabs of the two columns, and of the four groups of self-employed and previous employers, showed these rules.
- Likely effect: only the 120 real non-responses are candidates for imputation in Phase 6.

### Health-history columns (columns 45-54)
- What: `mh_disorder_past` and `mh_disorder_current` are ordinal (No, Maybe, Yes); `diagnosed_by_professional` and `sought_treatment` are binary; `family_history_mh` is nominal. In `interferes_when_treated` and `interferes_when_not_treated`, "Not applicable to me" (39% and 33%) most likely means no condition and is handled as a structural gap, not as a scale point. Only counts are printed; free-text columns are not printed at all.
- Why: sensitive health data (#15); the answers describe a voluntary sample.
- Likely effect: the shares are not prevalence rates (self-selection in a survey about mental health in tech). Ordinal for "Maybe" about a fact is the weakest use of the Maybe rule; the alternative is nominal.
- With more time: run both readings for the two disorder columns.
- Overlap (step 29b): the four health questions are strongly linked (89% of those with a current disorder are diagnosed; 73% give the same answer for past and current; 94% of the diagnosed have sought treatment). There are no logical contradictions to fix.
- Open: all four in the clustering (health would dominate the distances), one or a combined score, or none and use them only to describe the clusters (fits the HR focus). Phase 5.

### Location columns (columns 57-60)
- What: `country_live`, `country_work`, `us_state_live` and `us_state_work` are nominal. The state columns are missing exactly for people outside the US (593 and 582), which is structural.
- Why: 53 and 47 distinct values would create very sparse columns and small groups that can identify people.
- Open: group countries into a few regions and states into "US / not US" before encoding (Phase 5 and 6).

### Work position (column 61)
- What: `work_position` is a multi-select question stored as one string joined with `|`: 264 distinct values from 12 single roles; 817 respondents ticked one role, 616 ticked two to ten. It is recorded as nominal.
- Why: one-hot encoding the raw column would create up to 263 nearly empty columns and treat "Back-end" and "Back-end|Front-end" as unrelated.
- Open: split into one yes/no column per role (multi-hot encoding) in Phase 6.

### Condition lists (columns 48, 49, 51)
- What: the three condition columns are multi-select lists stored as one string joined with `|` (33, 23 and 34 single items). They are recorded as nominal and renamed to `_list`.
- Why: few distinct answers relative to the number filled, long answers and the separator showed the structure; 55-65% of answers contain a separator.
- Open: whether they enter the clustering, are used only to describe clusters (aggregated, minimum group size), or are left out (Phase 5, feature scope #6). They are special-category health data, an HR program should not target by diagnosis, and rare conditions form identifiable groups.

### Reasons for interview answers (columns 37, 39)
- What: the two "why" columns are real free text (almost every answer unique, up to 2,475 characters) and are left out of the analysis.
- Why: they would need text analysis, which is outside the scope of the report.
- With more time: text analysis of the reasons (for example keywords or topics).

### Gender (column 56)
- What: 1,430 answers in 70 variants (54 after removing spaces and capitals). The four most common variants cover 95%; 41 variants are used by one person each. Only the five most common variants were printed.
- Open: cleaning in Phase 6 (#15). Options: three categories (simple, but lumps identities and creates a small identifiable group), two categories (excludes gender-diverse people, hard to justify), or gender only to describe clusters (mapping errors cannot shape the clusters; HR should not target by gender).

### Country and state of living and of work (columns 57-60)
- What: location names are clean (no synonyms or typos). Country of living and of work differ for 26 of 1,433 respondents (1.8%), US states for 41 of 837 (4.9%).
- Open: keep only one country column and one state column (Phase 5 and 6). Work fits the HR question (employer, benefits and labour law follow the workplace); living fits access to treatment (healthcare follows residence).

### Age (column 55)
- What: `age` is ratio-scaled. Median 33, middle half 28 to 39. Step 28 found three impossible values (3, 99, 323), two unusual ones (15, 17) and one plausible high value (74).
- Plan: set 3, 99 and 323 to missing in Phase 6, so they count as non-response and the rest of each respondent's answers stays usable. Rejected: dropping the rows (loses 62 other answers per person for one wrong field) and guessing a value (no basis in the data).
- Open: keep 15 and 17 (possible, only two) or set them to missing (the survey targets working adults; minors' data raise an extra ethical question). Phase 6.
- Outliers: the 1.5 × IQR rule flags 35 respondents aged 55 to 74. They are real older workers and are not removed; the rule is used to find unusual values, not to decide about them.
- Open: if age is used, as a number or in age groups (groups reduce the influence of single values and the re-identification risk). Phase 6.
- Likely effect: without the three errors the standard deviation drops from 11.3 to 8.1, which matters for distance-based clustering.

### First plots (step 29)
- What: age histogram, a bar chart of the anonymity question, grouped bars of mental against physical health consequences, and a missing-value overview that splits each bar into "not asked" and "asked, not answered".
- Rules: plots show shares of those who were asked, state the base in the title (for example "employed, n = 1,146"), say what was left out (the three impossible ages), and keep answers in a logical order.
- Why: HR must be able to read the plots without the code (#7), and the missing-value plot must not make survey design look like data loss.
- Open: which figures go into the report, and saving them as files. Phase 10.

### Codebook (step 25b)
- What: `data/column_map.csv` has six columns: position, short name, original question, scale level, group and asked_of.
- Groups (content only): work_setting (columns 0-3, 24, 61, 62), workplace (4-15, 25-35), attitudes (18-21, 36-44), personal (16, 17, 22, 23, 45-54), demographic (55-60).
- Why: the feature-scope decision (#6) becomes a filter on the group column.
- Judgment calls: free text is marked by the scale level, not by a group, so `gender` stays demographic. Columns 16 and 17 are personal, because the self-employed have no employer (alternative: workplace, as mirrors of columns 4 and 7). Columns 36-44 form their own attitudes group (alternative: split into workplace and personal). Productivity (22, 23) is personal and remote work (62) is work_setting (alternative: both workplace).

### Who was asked (asked_of)
- What: `asked_of` records who a question was meant for, not who answered (13 rules, from "all" for 23 columns to single follow-up rules). Gaps beyond the rule are non-response and are counted in step 26.
- Why: keeps "not asked" and "not answered" apart (Rubin).
- Evidence: every rule was confirmed with a crosstab or a row-by-row check. The 12 employer columns with 287 gaps are missing exactly for the self-employed. The three condition free-text columns follow their gate question (current disorder Yes, Maybe; diagnosed Yes), with 7, 5 and 5 real non-responses.
- Limits: column 19 has no clean rule and is labelled an optional follow-up; column 5 has 133 extra gaps, mostly among people without benefits, counted as non-response.

### Missing values overall (step 26)
- What: missing values are reported per column split into "not asked" and "not answered", not as one share. 21,960 of 90,279 cells (24.3%) are missing; 20,810 of them (94.8%) are "not asked"; 1,150 are non-response, in 10 columns (two free text, three condition lists, gender and four structured columns).
- Why: a single missing share would make survey design look like data loss and would suggest dropping columns that are only answered by a subgroup.
- Likely effect: no column is dropped because of its missing share; whether subgroup columns stay is a feature-scope question (Phase 5).
- Open: "no answer" category or imputation for the three structured columns with non-response (Phase 6). Arguments: a category is simple, explainable for HR and treats skipping as meaningful; imputation is more principled when gaps depend on observed answers (as for `cur_knows_care_options`), but heavier and assumes the gaps can be predicted from other answers.
- The table is rebuilt by the notebook and not saved as a separate file.

### Observations (step 30)
- What: my observations are collected in a separate doc, "My own thoughts & observations (my words)", with the numbers, charts and sources behind each one and a section of checks where a statement goes further than the data. They are notes; the report text is written new in my own words.
- Why: the observations sit next to their evidence, and overstatements are caught before the report.
- Observations so far: who answered the survey; age; anonymity when using mental health resources; expected consequences of discussing mental against physical health; interference with work when treated or not treated effectively.

### Anonymity and resources (step 30)
- What: "I don't know" about anonymity (65%) is read mainly as "no program to be anonymous about": 615 of the 742 either say their employer offers no resources or don't know whether it does. Where resources exist, 54% think they are protected, 3% think they are not, and 43% don't know.
- Consequence: two levers for later: make support available and known in the first place, and, where it exists, explain how anonymity is protected.
- Limit: both questions are perceptions; "no resources" can also mean "I don't know about the resources".

### Need and access (step 30)
- What: my first reading, "the more people need the help, the less they can rely on it", was tested and not supported. People with a current disorder report resources about as often as others (29% against 24%) and benefits more often (57% against 38%). Missing resources are equally common in every group (45-49%); those without an issue more often don't know about benefits (33% against 19%).
- Consequence: the statement was corrected in the observations doc. The point for HR is that support is missing for almost half, whether they need it now or not, and that those who don't need it yet often don't know what exists.
- Open: "an often reported concern" needs a source if it goes into the report; otherwise it is phrased as my own expectation.

### Interference with work (step 30)
- What: interference "often" is reported by 7% when treated effectively and by 56% when not ("Not applicable to me" excluded).
- Limits: the wording "effectively" builds part of the gap in; self-reported; two different groups (876 and 965); not causal.

### Sample description (step 30)
- What: employed 80%, work in the US 59%, all company sizes represented, remote at least sometimes 77%, median age 33, gender about 73% male (before cleaning).
- Consequence: the US share is the concrete reason for the one sentence on the limits of representativeness: employer-provided health benefits mean something different under a statutory system. The high remote share is an argument for digital and anonymous ways into support (Phase 9).

## Open decisions (summary)

- Feature scope (#6): all questions, workplace questions only, or a hybrid. Phase 5.
- Role of dimensionality reduction (#12): visualization only or also before clustering. Phase 5 and 7.
- The eight self-employed-only columns (16 to 23): drop them or analyse the 287 self-employed separately. Phase 5.
- The 169 respondents without previous employers: drop the `prev_` block, own category, or cluster the 1,264 only. Phase 5 and 6.
- "I don't know" and "Not applicable" answers: one rule for all columns (own category, indicator column or missing). Phase 6.
- `role_is_tech` and `employer_is_tech`: merge into one "works in tech" feature or keep both. Phase 5 and 6.
- Countries and states: group into regions and "US / not US" before encoding. Phase 6.
- Age: set 3, 99 and 323 to missing; keep or drop 15 and 17; number or age groups. Phase 6.
- `work_position`: split into one yes/no column per role (multi-hot). Phase 6.
- `gender`: three categories, two categories, or only to describe clusters (#15). Phase 6.
- Condition lists (48, 49, 51): for clustering, only to describe clusters, or not at all. Phase 5.
- The four overlapping health questions: all four, one or a combined score, or only to describe clusters. Phase 5.
- Which observations and figures go into the report, and saving the figures as files. Phase 10.
- A source for "an often reported concern" (need and access), or rephrase it as my own expectation. Phase 10.
- Country and state: keep the work or the living columns, not both. Phase 5 and 6.
- Gower distance or k-modes instead of one-hot encoding plus k-means. Phase 6 and 8.
- Real non-response in `cur_knows_care_options`, `observed_unsupportive_response` and `observation_less_likely_reveal`: a "no answer" category or imputation. Phase 6.