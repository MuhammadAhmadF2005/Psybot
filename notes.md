**Data preprocessing (and modelling notes)**

[CLEANING]  Drop row 2038 - ghost row
[CLEANING]  Fix dirty Stress labels - remap 'Moderate' and 'High'
[CLEANING]  Drop 6 duplicates - keep first occurrence
[FEATURE]   Decide on Depression class collapsing - bottom two bins thin
[FEATURE]   Handle CGPA 'Other' - 171 rows, meaning unclear
[MODELLING] Use weighted F1, not accuracy - class imbalance present