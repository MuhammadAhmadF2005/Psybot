Categoricals — Key Observations

Age: 96.4% of records are 18–26. "Below 18" (4 rows) and "Above 30" (3 rows) are negligible fringe groups. When you encode this later, those two bins will be nearly meaningless as separate categories.

Gender: 69.4% Male, 30.1% Female, 0.5% "Prefer not to say." Heavy male skew — worth noting as a demographic limitation. That 0.5% "Prefer not to say" is too small to be statistically useful as its own category.

University: 15 universities, but heavily concentrated — IUB (26.1%) and AIUB (24.7%) together account for over half the dataset. The bottom 6 universities have fewer than 25 rows each. High cardinality with severe imbalance across categories.

Department: 69.5% are CS/engineering students. This is not a general student population — it's overwhelmingly a tech-student dataset. Your supervisor will probe this if you claim generalizability.

Academic Year: Reasonably distributed across years 1–4. "Other" at 72 rows is vague but not negligible.

CGPA: Concentrated in 3.00–3.79 range (56%). "Other" at 171 rows is a problem — that's an ambiguous category that needs inspection later. What does "Other" mean for CGPA?

Waiver/Scholarship: 78.7% No, 21.3% Yes. Clean binary, easy to encode.

# PsyBot — Data Inspection Observations

---

## 1. Dataset Overview
- **Total rows:** 2039
- **Total columns:** 39
- **Clean rows after planned fixes:** ~2032

---

## 2. Missing Values
- Exactly **1 missing value per column**, all from **row 2038**
- Row 2038 is a fully null ghost row — every single column is NaN
- Almost certainly an artifact from the merge between the base Syeed et al. dataset and the DIU extension
- Nothing to impute — straightforward drop

---

## 3. Duplicate Rows
- **6 duplicate rows** identified (3 pairs)
- All duplicates match completely across all 39 columns including demographics, every item score, totals, and labels
- One pair (rows 17/492 vs 712/763) differs only on `waiver_or_scholarship` — same record entered with a data inconsistency on one field, still a duplicate
- Almost certainly from the dataset merge — not genuine repeat respondents

---

## 4. Label Quality Issues

### 4a. PSS-10 (Stress) — Naming Inconsistency
- **10 rows** have dirty labels: `'Moderate'` (9 rows) and `'High'` (1 row)
- These are rows 2028–2037, concentrated at the end of the dataset — from the DIU extension
- Boundary check confirms values are correctly computed; only the label strings are inconsistent
- `'Moderate'` → should be `'Moderate Stress'`
- `'High'` → should be `'High Perceived Stress'`
- **Not a scoring error — purely a naming inconsistency from the merge**

### 4b. PHQ-9 (Depression) — Non-Standard Label
- **44 rows** labelled `'No Depression'` with score = 0
- Standard PHQ-9 uses a 5-class system where score 0–4 = `'Minimal Depression'`
- Dataset authors created a non-standard 6-class system by splitting Minimal into:
  - Score = 0 → `'No Depression'` (44 rows, 2.2%)
  - Score 1–4 → `'Minimal Depression'` (97 rows, 4.8%)
- Decision: **collapse to standard 5-class** by merging `'No Depression'` into `'Minimal Depression'`
- Combined bottom class will have 141 rows — still thin but more workable

### 4c. GAD-7 (Anxiety) — Clean
- **Zero mismatches** in boundary check
- Labels are fully consistent with standard GAD-7 scoring bands
- No action required

---

## 5. Class Distribution & Imbalance

### Stress Label (3 classes after fix)
```
Moderate Stress          1348 + 9  = 1357   ~66.7%
High Perceived Stress     565 + 1  =  566   ~27.8%
Low Stress                         115        5.6%
```
- Moderately imbalanced — Low Stress is thin at 5.6%
- Use weighted F1 for evaluation

### Anxiety Label (4 classes)
```
Severe Anxiety      744   36.5%
Moderate Anxiety    622   30.5%
Mild Anxiety        512   25.1%
Minimal Anxiety     160    7.8%
```
- Cleanest distribution of the three targets
- Minimal Anxiety is the thinnest class but still 160 rows — acceptable

### Depression Label (5 classes after collapse)
```
Moderately Severe Depression    512   25.1%
Severe Depression               505   24.8%
Moderate Depression             461   22.6%
Mild Depression                 420   20.6%
Minimal Depression              141    6.9%  ← merged No + Minimal
```
- Most balanced after collapse
- Bottom class still thinnest but 141 rows is workable

---

## 6. Demographic Observations

### Age
- 96.4% of records are aged 18–26 (university students)
- `'Below 18'` (4 rows) and `'Above 30'` (3 rows) are negligible fringe groups
- Dataset is effectively a young adult population only

### Gender
- 69.4% Male, 30.1% Female, 0.5% 'Prefer not to say'
- Heavy male skew — limits generalizability across genders
- Females show higher Severe Depression rates (30.2%) vs Males (22.2%) — consistent with existing literature
- Males show higher Low Stress rates (7.1%) vs Females (2.4%)

### 'Prefer Not to Say' Gender Group (n=10)
- Disproportionately high severity across all three scales:
  - Mean Stress: 28.6 (High Perceived Stress territory)
  - Mean Anxiety: 15.2 (Severe Anxiety threshold)
  - Mean Depression: 20.5 (Severe Depression territory)
- 60% Severe Anxiety, 60% Severe Depression
- Too small for separate modelling but notable as a demographic finding
- Consistent with research on higher mental health burden in gender-nonconforming individuals

### University
- 15 universities, heavily concentrated: IUB (26.1%) + AIUB (24.7%) = 50.8% of data
- Bottom 6 universities have fewer than 25 rows each
- High cardinality with severe imbalance — problematic as a model feature

### Department
- 69.5% CS/Engineering students
- Not a general student population — heavily tech-skewed
- Limits generalizability across academic disciplines

### CGPA
- 'Other' category: 171 rows — meaning ambiguous, requires further inspection
- Majority concentrated in 3.00–3.79 range (56%)

---

## 7. Scale Score Distributions

| Scale | Mean | Std | Min | Max | Clinical Note |
|---|---|---|---|---|---|
| PSS-10 (Stress) | 22.98 | 6.75 | 0 | 40 | Mean falls in Moderate Stress band |
| GAD-7 (Anxiety) | 12.32 | 5.49 | 0 | 21 | Mean at Moderate Anxiety threshold |
| PHQ-9 (Depression) | 14.40 | 6.66 | 0 | 27 | Mean just below Moderately Severe threshold |

- All three scales use their full range (min=0, max=scale ceiling)
- Dataset skews toward moderate-to-severe — reflects student population under academic stress
- **This is a population characteristic, not a data quality issue**

---

## 8. Population & Generalizability Limitations
- Bangladeshi university students aged primarily 18–26
- Heavily male (69%), heavily CS/Engineering (69%)
- Model trained on this data should not be assumed to generalize to:
  - Older adults
  - Non-student populations
  - Non-South-Asian cultural contexts
- These limitations must be stated explicitly in any deployment context
