**Data Preprocessing & Modelling Notes**

[CLEANING]  Drop row 2038 - fully null ghost row, artifact from dataset merge
[CLEANING]  Fix dirty Stress labels - remap 'Moderate' → 'Moderate Stress', 'High' → 'High Perceived Stress' (10 rows, DIU extension)
[CLEANING]  Drop 6 duplicates - exact matches across all 39 columns, keep first occurrence
[CLEANING]  Collapse Depression bottom two classes - merge 'No Depression' (44 rows) into 'Minimal Depression' (97 rows) → standard PHQ-9 5-class system, combined 141 rows

[INSPECTION]  Item-level variance per PSS/GAD/PHQ - skipped, revisit if needed
[INSPECTION]  Cross-scale crosstabs - skipped, revisit if needed
[INSPECTION]  CGPA 'Other' group - 171 rows, meaning unclear, resolve before feature engineering

[FEATURE]   Drop University & Department from feature matrix - high cardinality, population-specific, keep in dataset but exclude from model inputs
[FEATURE]   Ordinal encode Academic_Year and Current_CGPA - natural order exists
[FEATURE]   Binary encode Gender and waiver_or_scholarship
[FEATURE]   Handle Age fringe groups - 'Below 18' (4 rows), 'Above 30' (3 rows), collapse into adjacent bins or drop
[FEATURE]   Handle Gender 'Prefer not to say' - 10 rows, too small for own category, decision pending
[FEATURE]   Handle CGPA 'Other' - 171 rows, encode or drop pending inspection result
[FEATURE]   Decide feature matrix scope - individual item scores (PSS1-10, GAD1-7, PHQ1-9) vs aggregate scale values only vs both

[MODELLING] Three separate classifiers - one per condition (Stress, Anxiety, Depression), severity bands differ per instrument
[MODELLING] Use weighted F1, not accuracy - class imbalance present across all three targets
[MODELLING] Stratified K-Fold cross-validation
[MODELLING] Candidate models - Logistic Regression (baseline), Random Forest, XGBoost
[MODELLING] SMOTE - only if weighted F1 on minority classes remains poor after cleaning, do not apply preemptively

[README]    Update Depression class count from 6 to 5 after collapse
[README]    Add University/Department exclusion rationale to feature engineering section