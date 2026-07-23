**Data Preprocessing & Modelling Notes**

[CLEANING]  ✓ Drop row 2038 - fully null ghost row, artifact from dataset merge
[CLEANING]  ✓ Fix dirty Stress labels - remap 'Moderate' → 'Moderate Stress', 'High' → 'High Perceived Stress' (10 rows, DIU extension)
[CLEANING]  ✓ Drop 6 duplicates - exact matches across all 39 columns, keep first occurrence
[CLEANING]  ✓ Collapse Depression bottom two classes - merge 'No Depression' (44 rows) into 'Minimal Depression' (97 rows) → standard PHQ-9 5-class system, combined 141 rows
[CLEANING]  ✓ Export cleaned dataset to mental_health_cleaned.csv

[EDA]       ✓ Cell 1: Distribution plots (Histograms + KDE with threshold annotations)
[EDA]       ✓ Cell 2: Class distributions (Countplots with counts & % annotations)
[EDA]       ✓ Cell 3: Inter-scale correlation (Pearson heatmap & pairwise scatter plots)
[EDA]       ✓ Cell 4: Item-level analysis (Boxplots per instrument & item response variance bar chart)
[EDA]       ✓ Cell 5: Demographic splits (Normalized stacked bar charts & age boxplots)
[EDA]       ✓ Cell 6: Comorbidity heatmaps (Cross-scale severity crosstabs)
[EDA]       ✓ Cell 7: CGPA 'Other' group inspection (Mean score comparison & severity breakdown)
[EDA]       ✓ Composite dashboard generation (eda_plots_combined.png)
[EDA]       ✓ Document visualization findings in observations.md

[FEATURE]   ✓ Step 1: Drop University, Department, and aggregate scale totals (Stress/Anxiety/Depression Values)
[FEATURE]   ✓ Step 2: Mode impute Gender 'Prefer not to say' (10 rows → 'Male')
[FEATURE]   ✓ Step 3: Handle Age fringe groups - collapse 'Below 18' → '18-22', 'Above 30' → '27-30'
[FEATURE]   ✓ Step 4: Ordinal encode Academic_Year and Current_CGPA as int64 with median value 2 for 'Other'
[FEATURE]   ✓ Step 4: One-hot encode Gender, Age, waiver_or_scholarship with drop_first=True
[FEATURE]   ✓ Step 5: Verify item score dtypes - cast all 26 item scores (PSS1-10, GAD1-7, PHQ1-9) to int64
[FEATURE]   ✓ Step 6: Define feature matrix X (32 features) & target series (y_stress, y_anxiety, y_depression)
[FEATURE]   ✓ Step 7: Export all output CSVs (X_features.csv, y_stress.csv, y_anxiety.csv, y_depression.csv, mental_health_engineered.csv)
[FEATURE]   ✓ Step 8: Final pipeline summary and verification in feature_engineering.ipynb

[MODELLING] ✓ Step 1: Load feature matrix X and target Series (y_stress, y_anxiety, y_depression)
[MODELLING] ✓ Step 2: Stratified 70/30 train/test split with aligned indices across targets (random_state=42)
[MODELLING] ✓ Step 3: Logistic Regression baseline with StandardScaler (best for Stress: 0.9822, Anxiety: 0.9625, Depression: 0.9641)
[MODELLING] ✓ Step 4: Random Forest classifier on unscaled features (Stress: 0.9147, Anxiety: 0.9128, Depression: 0.8328)
[MODELLING] ✓ Step 5: XGBoost classifier on encoded integer labels (Stress: 0.9337, Anxiety: 0.9275, Depression: 0.8481)
[MODELLING] ✓ Step 6: Comparative Weighted F1 summary table
[MODELLING] ✓ Step 7: Stratified 5-Fold Cross Validation on full dataset (Stress CV: 0.9750, Anxiety CV: 0.9839, Depression CV: 0.9711)
[MODELLING] ✓ Step 8: Export serialized model artifacts (best_model_stress.pkl, best_model_anxiety.pkl, best_model_depression.pkl, scaler.pkl)

[README]    Update Depression class count from 6 to 5 after collapse
[README]    Add University/Department exclusion rationale to feature engineering section