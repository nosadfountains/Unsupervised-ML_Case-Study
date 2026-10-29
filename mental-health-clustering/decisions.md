# Decisions and omissions

This file documents the decisions and simplifications behind `analysis.ipynb`: what was decided, why, the likely effect and, where relevant, what I would do with more time. It is the basis for the critical reflection in the report.

## Preparation: Working environment

- **Python 3.14** (python.org) is used. The installed 3.15 alpha is not, because scikit-learn and matplotlib did not support it yet.
- **No virtual environment:** to keep the setup simple, I installed all packages directly into Python 3.14 and always call it as `python3.14` or `python3.14 -m pip`, so that the 3.15 alpha cannot be used by mistake. The package versions are listed in `requirements.txt`.
- **Tools:** VS Code on Mac (iOS) (`analysis.ipynb`). pandas 3.0 is used.

## Phase 1: Understanding the problem

- **Task and data:** Task 1 (mental health in technology-related jobs) with the OSMI Mental Health in Tech Survey 2016 from Kaggle. Only the CSV file is used (1,433 respondents, 63 questions).
- **Representativeness:** as the task states, the survey counts as representative of the company's staff. Since this is a premise of the case, the report spends only one sentence on self-selection and the US-heavy sample.
- **What the results can show:** the clusters describe who answers similarly, not what causes mental health problems, and the survey is a single snapshot. Hence, the levers for HR are presented as hypotheses backed by literature. The report also names the limits of a workplace program, as mental health depends on factors outside the employer's control as well.
- **Industry context:** I describe the respondents as sharing an industry context, not shared values, because "technology-oriented" describes the field of work rather than attitudes.
- **HR as the audience:** few segments with neutral names, plots with a short reading aid and the explained variance, and recommendations sorted by effort and impact. Long-term structural measures are marked as such, since quick measures can stay superficial.
- **Two kinds of concept:** the technical approach is described in the methodology, the organizational implementation concept for HR in chapter 4.
- **Order of methods:** exploration, planning, preprocessing and feature engineering, dimensionality reduction, clustering, evaluation, interpretation, and iteration. Only the iterations that changed the result are documented in the report; the rest stays in the notebook and the commit history.
- **Number of clusters:** there is no ground truth, and the task does not give k. Therefore, k is justified with metrics and with how interpretable the segments are.
- **Ethics:** health data are special-category data under the GDPR, and small groups can identify people. Hence, results are reported only in aggregate and for groups of at least 10 people (fixed in Phase 9), segment names are neutral, and the segments serve only to design the program, never to identify individuals.
- **Publishing:** the unchanged CSV is stored in `data/raw/` under its original file name; the license (CC BY-SA 4.0) and OSMI are credited in the README. Neither the task PDF nor the task text is published, as IU holds the copyright.
- **Writing:** figures in the introduction need citable sources, and omissions are logged in this file.

## Phase 2: Sources

- **What is cited:** peer-reviewed or primary sources in APA 7th edition, following the IU citation guidelines. 
- **Source set:** 12 core sources plus the GDPR, covering mental health at work, the Job Demands-Resources model, the dataset, the methods, missing data, ethics, and workplace interventions. Several intervention sources stem from one research network (Harvey, Joyce, and Gayed), which the report mentions in one sentence.
- **Methods:** Rousseeuw (1987) for the silhouette coefficient; Gower (1971) and Huang (1998) for distances and clustering with categorical data. As I used one-hot encoding with PCA and k-means, Gower distances remain a comparison for "with more time".
- **Recommendations:** structured by prevention level (Joyce et al., 2016), with the evidence ratings of WHO (2022).

## Phase 3: Data

- The raw CSV stays unchanged; all cleaning happens in the notebook, so everything can be reproduced.

## Phase 4: Exploratory data analysis

### Column names and codebook
- All 63 columns were renamed by hand to short names: prefix `cur_` for the current employer and `prev_` for previous employers, suffix `_self_emp` for questions only the self-employed were asked, `_list` for multi-select lists, and `_text` for free text.
- The codebook `data/column_map.csv` records for each question its short name, the original question, the scale level, a content group (work setting, workplace, attitudes, personal, demographic), who was asked (`asked_of`) and, since Phase 5, the decision to keep or drop it with a reason. The content groups set the order of the segment profiles (workplace first).
- Lesson learned: check who was asked a question before naming the column. Column 17, for example, looked like a question for everyone but was asked only of the self-employed.

### Who was asked and missing values
- `asked_of` records who a question was meant for, not who answered (13 rules, each confirmed with a crosstab). Gaps outside these rules count as non-response. Two exceptions: column 19 is an optional follow-up for the self-employed, and `cur_knows_care_options` has 133 gaps beyond the self-employed, mostly from employees without benefits (72) or not eligible (55).
- 21,960 of 90,279 cells (24.3%) are empty, but 20,810 of them (94.8%) are questions the skip logic never asked. Only 1,150 cells in 10 columns are non-response. Hence, no column was dropped because of its missing share, and missing values are always reported as "not asked" and "not answered" separately.
- Eight questions (16–23) were asked only of the 287 self-employed, `role_is_tech` only of the 263 employees at non-tech employers, and the 11 `prev_` questions only of the 1,264 respondents with previous employers (without exception). None of them were imputed (see Phase 5).

### Scale levels and answer scales
- Scale levels were assigned per column by judgment and stored in the codebook: "Yes / No / I don't know" as nominal, "Yes / Maybe / No" as ordinal, company size as ordinal, and two-value columns as binary. Since all columns were one-hot encoded in the end (Phase 6), the scale levels set the order of the answers in tables and profiles but do not enter the encoding.
- Judgment calls that matter for reading the results:
  - "I don't know" is not a midpoint and is informative in itself (742 of 1,146 employees chose it for anonymity).
  - "Yes" is the bad answer in the consequence questions but the good answer in the comfort questions.
  - In the five-point questions on career harm and coworkers' views, answers that report experience ("Yes, it has", "No, it has not") sit at the ends of the scale.
  - Previous-employer questions use None / Some / All, current-employer questions Yes / No / "I don't know", so the two blocks are not compared by column name. Observed negative consequences (40% at previous employers, 9% at the current one) are not compared as rates either, since the previous-employer question covers several employers.
  - The answers of the self-employed on revealing a condition to clients and coworkers measure perceived rather than experienced impact.

### Single columns
- **Free text and privacy:** the two "why" answers, the condition lists, and gender were inspected only with counts, not by printing individual answers (one exception: the first look at five raw rows, see Phase 10).
- **Multi-select lists:** the three condition lists (33, 23, and 34 single items) and the work position (12 roles in 264 combinations) are stored as text joined with "|" and were split in Phase 6.
- **Gender:** 70 spellings; the four most common cover 95%, and 41 are used by one person each.
- **Location:** the names are clean. Country of living and country of work differ for 26 of 1,433 respondents (1.8%), the US states for 41 of 837 (4.9%).
- **Age:** median 33 (middle half 28 to 39). Three values are impossible (3, 99, 323) and two unusual (15, 17). The 1.5 × IQR rule flags 35 respondents aged 55 to 74; as they are plausible older workers, they were not removed.
- **Health questions:** past and current disorder, diagnosis, and treatment are strongly linked (89% of those reporting a current disorder are diagnosed, and 94% of the diagnosed have sought treatment). "Not applicable to me" in the two interference questions most likely means "no condition". Since the sample is self-selected, the shares are not prevalence rates.
- **Figures:** plots show the shares of those who were asked, name the base in the title (e.g., "employees, n = 1,146"), and keep the answers in a logical order.

### Exploratory findings
- **Anonymity:** 65% of the employees do not know whether their anonymity would be protected, and 615 of these 742 say that their employer offers no resources or that they do not know of any. Where resources exist, 54% consider their anonymity protected. This points to two levers: making support available and known, and explaining how anonymity is protected. Both questions measure perceptions.
- **Mental vs. physical health:** 19% of the employees expect negative consequences from discussing a mental health issue with their employer, but only 4% from discussing a physical one.
- **Need and access:** my expectation that those who need help most can rely on it least was not supported. Employees who report a current condition mention resources (29% vs. 24%) and benefits (57% vs. 38%) more often than the others, and missing resources are about equally common in all groups (45% to 49%).
- **Interference with work:** 7% report frequent interference when treated effectively, 56% when not. These are self-reports from two different groups, not a causal effect, and the word "effectively" builds part of the gap into the question.
- **Sample:** 80% employees, 59% work in the US, median age 33, 77% work remotely at least sometimes, and about 73% male (before cleaning). The US share matters, because employer health benefits mean something different under a statutory health system.

## Phase 5: Planning the approach

- **Feature scope:** I kept all questions except the two free-text "why" answers (61 of 63), so that the structure comes from the data rather than from my preselection. The task does not prescribe a selection.
  - Rejected: workplace questions only; workplace questions for the clustering with personal factors only for the profiles.
  - Effect: health and the survey path can dominate, and the encoding creates many sparse columns.
  - With more time: a workplace-only clustering as a comparison.
- **Gaps from the skip logic:** kept as a category of their own ("not asked"; "no previous employer" for the 169 respondents without one) instead of being imputed, since imputing would invent answers to questions that were never asked. As a result, the survey path enters the data many times, which is handled in Phase 6. With more time: separate analyses of the self-employed and of the respondents with previous employers.
- **Health questions:** all four (past and current disorder, diagnosis, treatment) enter unchanged, although health is thereby counted about four times. With more time: one combined health score.
- **Condition lists and locations:** both enter the clustering (refined in Phase 6: rare conditions grouped, countries grouped into regions).
- **Tech employer and tech role:** `employer_is_tech` and `role_is_tech` stay separate, as merging them would need a rule of its own.
- **Dimensionality reduction:** applied before the clustering, with PCA on the one-hot encoded data, because the many sparse columns make distances less informative and the PCA loadings can be explained. As PCA treats categorical data only approximately, MDS on Gower distances remains a comparison for "with more time". Since clusters cannot be read in component space, the segments are described with the original answers.
- **Keep or drop per column:** the codebook holds a decision and a one-line reason for each question (61 kept, 2 dropped). Kept after a closer look: `self_employed` and `has_prev_employers` (they repeat the "not asked" pattern of their blocks), `family_history_mh`, `reveal_clients_neg_impact_self_emp` (the weakest column: 143 of 287 self-employed did not answer it), and `gender`.

### Final pipeline
1. Load the raw CSV and rename the columns with the codebook.
2. Clean: drop the two "why" columns, map gender, split the multi-select lists, group ages and countries, and label the gaps "not asked" or "no answer".
3. Encode all text columns one-hot without scaling, add two domain scores, and remove exact copies and opposites.
4. Reduce the dimensions with PCA (MDS for the 2D view).
5. Cluster with k-means, compare with a Gaussian mixture model (GMM) and Ward clustering, and check the stability.
6. Describe the clusters with the original answers, name them, and derive levers for HR.

## Phase 6: Preprocessing and feature engineering

- **Dropped columns:** `df_clean` is a working copy without the two "why" columns (1,433 × 61); `df` stays unchanged. I set no threshold for missing values, as most gaps are "not asked". A column would count as near-constant if one value (including "not asked") covered 95% of all rows; none did (highest: 0.900). Within its subgroup, `role_is_tech` is almost constant (248 of 263 "Yes"), which this rule does not catch.
- **Gender:** mapped to three categories by a rule ("f" or containing "female" or "woman" becomes female; then "m", "male", or "man" as a word becomes male; everything else becomes other): male 1,054, female 342, other 34, empty 3. Three categories keep gender-diverse respondents in the data, and the rule avoids reading individual answers. However, the rule cannot be checked answer by answer, and "other" (29 spellings) probably mixes gender-diverse answers with typos. With more time: review the "other" spellings by hand.
- **Condition lists:** split into one 0/1 column per condition. As 54 of the 90 single conditions were named by fewer than five people, I grouped all conditions named by fewer than 10 people into one `other_condition` column per list. Result: 32 condition columns plus 3 `other_condition` columns (1 for 28, 23, and 32 people). Very different conditions share one column, and spelling variants were not merged.
- **Work position:** split into 12 role columns. 817 respondents name one role and 616 two to ten; the 15 with seven or more roles sit far from everyone else in distance-based methods.
- **Age:** the impossible values were set to missing, 15 and 17 were kept (plausible, e.g., interns), and age was grouped into four bands: under 25 (125), 25–34 (710), 35–44 (442), 45 and over (153), missing 3. Bands reduce the influence of single values and the risk of re-identification. However, differences within a band are lost, and the band limits are my choice.
- **Missing values:** I did not impute but labeled every gap as "not asked" (18,129 cells) or "no answer" (491 cells), based on the `asked_of` rules. In the condition lists, empty rows became 0, plus two indicator columns per list. For the skip-logic gaps, this treats the survey path as information instead of inventing answers. For the non-response, the gaps can be related to the answers (in `cur_knows_care_options` mainly to having no benefits), so they stay visible as a category (Rubin, 1976). With more time: imputation (e.g., `IterativeImputer`) as a comparison.
- **Regions:** countries were grouped into five regions (US, Canada, UK, rest of Europe, rest of world; "Other" counts as rest of world, Russia as Europe; checked in the notebook), and the two US state columns were dropped, as they would only repeat the US region. This changes the Phase 5 decision, and differences within a region (e.g., labor law and health systems within Europe) are lost.
- **Encoding:** all 52 text columns were one-hot encoded, so every answer, including "not asked", "no answer", "I don't know", and "Not applicable", became a 0/1 column (286 columns in total). This needs no artificial order and no aligned "Yes" direction. On the other hand, the order of the answers is lost, and cluster centers become fractions that match no real answer (Huang, 1998).
- **No scaling:** all columns are already 0/1, so standardizing would only give rare answers the largest weights. In return, rare answers hardly influence the components (66 columns have a 1 for fewer than 5% of the respondents). With more time: weighting each question equally (multiple correspondence analysis).
- **Domain scores:** two scores from 0 to 1.
  - Known support is the share of four support offers answered "Yes" (benefits, resources, formal discussion, anonymity; "I don't know" counts as 0; employees only; mean 0.30). It describes the segments but is not a clustering input.
  - Perceived stigma averages expected career harm, coworkers' negative view, and not raising mental health in a job interview (all respondents; mean 0.62). It is part of the input, next to its source columns.
  - Equal weights are my choice, and stigma is counted twice, though only modestly, as its spread is small.
- **Redundancy:** 38 columns that were exact copies or opposites of others were removed, keeping answer columns in preference to "not asked" columns. Without this removal, being self-employed or not would have entered the data about 22 times and having previous employers about 12 times. Nine near-duplicate pairs (e.g., region of living vs. region of work) were left to PCA. No pair of current- and previous-employer columns correlates at 0.9 or more, so both blocks stay; however, this only checks pairs, not whole blocks. Result: `X` with 1,433 rows and 249 columns.

## Phase 7: Dimensionality reduction (iteration 1, all respondents)

- **Number of components:** the PCA spreads the variance thinly: PC1 explains 7.7%, PC2 5.9% (13.6% together), and 50%, 70%, 80%, and 90% need 22, 47, 65, and 94 components. As no rule is clearly right for categorical survey data, I carried two candidates forward: 4 components at the bend of the scree plot (about 22%) and 47 for 70%.
  - Rejected: eigenvalues above 1 (meaningful only for standardized data); 80% or 90% (too many dimensions again); parallel analysis (no source in the source set; with more time).
  - Silhouette scores are not compared between the candidates, as they tend to look better in fewer dimensions.
- **What the components mean:** PC1 reflects health status (mean score +1.36 for "no current disorder", −1.37 for "yes"), PC2 separates the self-employed from the employees (+2.48 vs. −0.62), and PC3 openness versus perceived stigma. The current employer's support has no component of its own among the first seven. This suggests that topics asked through many overlapping columns dominate: health through four questions, two interference questions, and 35 condition columns, and self-employment because all employer questions become "not asked" at once.
- **MDS for the 2D view:** non-metric MDS on the same `X` serves as a check of the PCA view, not as a clustering input. Its 2D view keeps the order of the distances better (rank correlation 0.70 vs. 0.56 for the PCA view; Stress-1 0.32), partly because this is what MDS optimizes. Both views show the same two patterns, but PCA makes the gap between the self-employed and the employees look sharper. Hence, the figure shows the PCA view (main view, explainable axes) and the MDS view (check) side by side.
  - Rejected: LLE (many identical distances in 0/1 data make the neighbor search unstable); MDS on Gower distances (a different input would make the comparison unfair).

## Phase 8: Clustering

- **Iteration 1, all respondents:** k-means with k = 4 as a first guess (two main splits, four combinations), ten random starts, and a fixed seed (`random_state=42`) on both candidates. All 287 self-employed formed one cluster of their own, and the employees were split mainly by health; the two candidates agreed for 97% of the respondents. The two clusters of employees who mostly report a current condition differ in known support (0.44 vs. 0.22) and perceived stigma (0.55 vs. 0.78), which is the first pattern HR could act on. As the answers behind the support score are part of the input, this difference describes the clusters rather than confirming them.
- **Iteration 2, employees only:** I restricted the analysis to the 1,146 employees, as the company's staff are employees and the self-employed have no employer to act on. Additionally, the survey path would have dominated the choice of k. The 32 columns with only one value among employees and one column each of two pairs that became exact opposites were removed (`X_emp`, 1,146 × 215), and the PCA was fitted again. The self-employed remain the separate group found in iteration 1.
  - Rejected: continuing with all respondents; employees with health used only for the profiles (a possible iteration 3).
- **Components in iteration 2:** PC1 health status (r = −0.73 with a current disorder), PC2 openness versus stigma (r = −0.65 with perceived stigma, +0.44 with known support), PC3 support at previous employers together with working in the US, PC4 uncertainty ("Some", "Maybe", and "I don't know" answers), PC5 having seen mental health issues handled poorly, and PC6 working in the US mixed with current support. PC1 and PC2 explain 8.2% and 5.6% (13.8% together).
- **Six components:** the scree plot bends after three (17.6%) and after six components (26.4%), so I let the content decide. Three components represent only about 30% of known support, six about 66%, while health (63% vs. 67%) and stigma (56% vs. 57%) hardly change. As known support is HR's main lever, I chose six; 44 components (70%) serve only as a stability check. However, k-means weighs each component by its variance, so health (8.2%) still counts about three times as much as PC6 (2.7%), which partly reflects location. PC4 may partly be an answering habit.
- **Number of clusters:** k-means for k = 2 to 8. There is no clear elbow, and the silhouette coefficient is highest for k = 2 (0.229) and 0.164 for k = 4. All values stay below 0.25, which suggests a continuous range of answers rather than separate types. As the measures could not decide, I compared the profiles and chose k = 4, the smallest k that splits both health groups into a more open and a more wary part. From k = 4 on, the prevention group appears (segment D: no current condition, high perceived stigma, little known support), the most relevant group for a preemptive program.
  - Rejected: k = 2 (health only, which HR cannot act on); k = 3 (hides the prevention group); k = 5 (adds an "uncertain" cluster that partly reflects an answering habit, is harder to communicate, and is less stable).
- **Method comparison:** a GMM and Ward's hierarchical clustering on the same six components. The memberships agree only moderately (adjusted Rand index, ARI, 0.43 and 0.42; about 71% in the matching cluster), and 15% of the employees sit between two GMM clusters. However, all three methods find the same two-by-two pattern of health and openness versus stigma: perceived stigma is highest in C and lowest in A, known support is highest in B, and every method finds the prevention group. The prevention group is the least stable (52% to 54% matched). k = 3 would give more stable memberships (ARI 0.64 and 0.50) but hide the prevention group, so I kept k = 4 and describe the segments as tendencies with approximate sizes. As Ward and k-means both minimize the spread within clusters, the GMM is the stronger test. The BIC points to no clear k.
- **Final model:** k-means with k = 4 on six components (`n_init=10`, `random_state=42`). Across ten other seeds, the ARI is 0.97 to 1.00; with 44 components, it is 0.89 (96% identical). Hence, the moderate agreement with GMM and Ward stems from the method, not from chance or the number of components. The smallest cluster holds 259 employees.
- **Figure:** `figures/clusters_2d.png` shows the PCA view and the MDS view side by side. Color shows openness (blue more open, orange more wary), shape the health context (circles mostly without, triangles mostly with a current condition), and the letters A to D mark the cluster centers. The two colors were checked with a color-vision simulation, and the shapes keep the clusters apart in print. In the PCA view, the clusters form a grid with straight boundaries, as k-means cuts a continuous cloud; the MDS view, computed independently of the clustering, shows the same four regions with blurred boundaries. The caption needs the 13.8% of variance, the note that the clustering used six components, the MDS fit (Stress-1 0.32; rank correlation 0.71 vs. 0.54 for PCA), and the note that the segments overlap.

## Phase 9: Interpretation and recommendations

- **Segments with the original answers:** the cluster labels and both scores were attached to the employees' readable answers (`segments`, 1,146 × 112): A 259, B 321, C 285, D 281.
- **Minimum group size:** answers, health details, conditions, and locations are reported per segment only where at least 10 people share them (`min_group = 10`), the same threshold as for the condition columns. Rejected: 5 (higher risk of re-identification) and 20 (hides most rare conditions). The company's own survey uses the same threshold; the remaining risk that colleagues recognize each other in groups of 10 to 19 is named in the report.
- **Profiles:** shown workplace first, as a plain list of distinctive answers would lead with diagnoses. Key workplace answers (share of employees in %; "–" means fewer than 10 people):

| Answer | A | B | C | D | All |
|---|---|---|---|---|---|
| Employer provides mental health benefits | 36 | 69 | 43 | 33 | 46 |
| Knows the care options | 18 | 46 | 29 | 10 | 27 |
| Employer has discussed mental health | 26 | 26 | 14 | 14 | 20 |
| Employer offers resources | 24 | 42 | 17 | 17 | 26 |
| Anonymity is protected | 35 | 41 | 17 | 17 | 28 |
| Leave is easy (very or somewhat) | 73 | 57 | 17 | 30 | 44 |
| Expects consequences for raising mental health | – | 7 | 45 | 25 | 19 |
| Comfortable talking to the supervisor | 68 | 58 | 11 | 12 | 37 |
| Thinks it would hurt the career | 12 | 20 | 63 | 62 | 39 |

- **Interpretation:**
  - Being wary is not only a personal attitude: C and D also report that the employer communicates and provides less. Hence, openness is a lever for HR.
  - The stigma is specific to mental health (in C, 45% expect consequences for mental health but 11% for physical health).
  - Not knowing is widespread (55% to 73% do not know whether anonymity is protected) and strongest in D (10% know the care options).
  - C combines a wary climate with own negative experiences (22% have experienced an unsupportive response) and difficult leave (55%).
  - B and C report almost the same mix of conditions, so the difference between them lies in the workplace, not in the condition.
  - D is the prevention group: no current condition, but more often unsure about their own health (33% "Maybe"), uninformed, and expecting career harm.
  - B is 80% US-based, so part of its known support reflects US employer health insurance. Therefore, the levers focus on what transfers across health systems: openness, stigma, knowing what exists, anonymity, and leave.
  - Gender and age describe the segments but are not targets for measures. The wary segments more often never work remotely (35% and 38% vs. 19% in A and B), so support also has to reach on-site staff (an association only).
  - Limits: perceptions, not verified facts; associations, not causes; descriptive profiles without significance tests.
- **Names:** A – Comfortable and confident; B – Open and supported; C – Wary, with little support; D – Cautious and not yet reached. The names refer to the workplace, and health is stated in each description instead, because nobody should be labeled by health and B and C differ in the workplace, not in their conditions. "Not yet reached" places the information gap with the organization rather than with the people; "at risk", "closed", and "fearful" were avoided as stigmatizing.
  - Rejected: names that include health (they label people); letters only (hard for HR to remember).
  - Before using the names, the company should check them with HR and employee representatives.
- **Levers:** the segments set priorities but do not target individuals. Measures are offered company-wide with an emphasis per segment and address workplace conditions and access to help. Each measure is a hypothesis with its evidence strength (WHO, 2022, GRADE ratings as strength / certainty; Joyce et al., 2016).
  - A: make existing offers and rules known (benefits, care options, anonymity, leave), as stigma hinders the use of available support (WHO, 2022, p. 2). Primary prevention.
  - B: reasonable accommodations arranged without identifying the person to colleagues (WHO rec. 3: strong / very low; privacy p. 21) and a structured return to work (WHO rec. 11: conditional / low; Joyce et al., 2016, p. 688). Tertiary prevention.
  - C: manager training on knowledge, attitudes, and how to respond (WHO rec. 4: strong / moderate, the best-supported measure, p. xi; Brohan et al., 2012, pp. 8–9) and a clear, confidential process for leave and accommodations with an explicit commitment that raising mental health brings no disadvantage (WHO rec. 3 and 11; Brohan et al., 2012, p. 5). Secondary and tertiary prevention.
  - D: mental health literacy and anti-stigma measures with visible leadership statements (WHO rec. 6: conditional / very low), low-threshold and confidential ways into support that also reach on-site staff, and an optional stress-management offer (WHO rec. 8A: conditional / low; Joyce et al., 2016, p. 688). Primary and secondary prevention.
  - Order: quick wins are making offers known and manager training (top priority because of its evidence); medium term a confidential leave and accommodation process and literacy and anti-stigma offers; long term organizational changes to psychosocial risks, developed with employees (WHO rec. 1: conditional / very low; Joyce et al., 2016, p. 693).
  - Overall logic: a balanced mix of primary, secondary, and tertiary measures (Joyce et al., 2016, p. 694) that strengthens job resources, which can buffer the effect of demands (Bakker & Demerouti, 2007, pp. 314–315, 322).
  - Avoided: counseling or employee assistance programs as the main lever (limited evidence; Joyce et al., 2016, pp. 688, 693), screening or using the segments to identify people (WHO, 2022, pp. 70–71), and copying US-style benefits.
  - Caveats: the intervention evidence leans on one research network, "very low certainty" means the estimates may change, and a pilot should measure both symptoms and work outcomes, as they do not necessarily move together (Joyce et al., 2016, p. 693).
- **Critical reflection, main points for chapter 5:**
  1. Robustness: low silhouette scores (0.164 for k = 4; all below 0.25), so the segments cut a gradient. The profiles replicate under three methods, but the memberships agree only moderately (ARI 0.42 to 0.43), and the prevention group is the least stable (52% to 54%). Within k-means, the result is stable (seeds 0.97 to 1.00; 6 vs. 44 components 0.89), and the MDS view shows the same four regions.
  2. Representativeness: a voluntary web survey (Bethlehem, 2010, p. 173) with a US-heavy sample (59% of all respondents and 62% of the employees work in the US), so the shares are not prevalence rates. Self-selection probably over-represents people with own experience.
  3. Preprocessing and reduction: topics with many overlapping columns dominate (health); PCA on categorical data is only an approximation; the PCA 2D view keeps 13.8% of the variance and makes the gaps look sharper. Further judgment calls: no imputation, one-hot encoding without order, no scaling, the domain scores, the gender rule, the age bands, and the regions.
  4. Meaning: perceptions, self-reported, at one point in time; associations, not causes; reverse effects are possible, as strained employees may perceive fewer resources (Bakker & Demerouti, 2007, p. 321). The profiles have no significance tests, and the levers are hypotheses.
  5. Ethics: health data are a special category (GDPR Art. 9), and small groups can identify people (Rocher et al., 2019, p. 5). Hence, the minimum group size of 10 applies, the remaining risk in groups of 10 to 19 is named, and the segments are used only to design the program (WHO, 2022, pp. 70–71; GDPR Art. 22). The self-employed are not covered by the levers.
  6. With more time: an iteration 3 with health used only for the profiles; Gower distances or multiple correspondence analysis; bootstrap stability and GMM membership probabilities per segment; imputation as a comparison; text analysis of the two "why" answers; a separate analysis of the self-employed; parallel analysis for the number of components; broader intervention evidence (LaMontagne et al., 2014).

