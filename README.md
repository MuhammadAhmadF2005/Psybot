# PsyBot: Psychological Screening & Monitoring Chatbot

PsyBot is an intelligent, memory-enabled chatbot system designed to interact with users, screen for common mental health conditions, maintain structured patient histories, and guide them with personalized psychoeducation and coping strategies at each stage of their journey.

---

## Project Scope & Core Ideas

- **Auth Layer:** Secure registration and user authentication via login credentials.
- **Screening Layer:** Conversational onboarding that administers validated screening instruments to assess the user's current mental health state across three conditions.
- **Memory Layer:** Long-term conversation and session history powered by LangChain with a relational database backend (PostgreSQL).
- **Advising Layer:** Context-aware dialogue during active sessions providing psychoeducation, evidence-based coping strategies, and referral triggers when severity thresholds are crossed.
- **Reevaluation Layer:** On subsequent logins, the system compares current screening scores against prior session history to track progression, improvement, or deterioration over time.

> **Important Boundary:** PsyBot screens and monitors — it does not diagnose. All outputs are severity classifications based on validated instruments. Clinical diagnosis remains the responsibility of a licensed professional. When scores cross clinical thresholds, the system escalates to a referral prompt rather than a clinical recommendation.

---

## Conditions Screened

PsyBot screens and monitors for three conditions, each measured by a validated, widely-used clinical instrument:

| Condition | Instrument | Items | Severity Bands |
|---|---|---|---|
| Stress | PSS-10 (Perceived Stress Scale) | 10 items | Low / Moderate / High |
| Anxiety | GAD-7 (Generalized Anxiety Disorder Scale) | 7 items | Minimal / Mild / Moderate / Severe |
| Depression | PHQ-9 (Patient Health Questionnaire) | 9 items | None / Mild / Moderate / Moderately Severe / Severe |

---

## Dataset & Sources

### Data Source
- **Primary:** Extended Mental Health Dataset for Anxiety, Stress, and Depression among University Students
- **DOI:** [10.5281/zenodo.17389336](https://zenodo.org/records/17389336)
- **Base dataset:** Syeed et al. (2024), DOI: 10.6084/m9.figshare.25771164.v1
- **Extended with:** DIU-collected responses (2025)
- **License:** CC BY 4.0
- **Final size:** ~2,038 records

**note:** University and Department columns were excluded from model features due to high cardinality and dataset-specific population bias. The model is scoped to individual symptom responses and aggregate scale scores, which are population-agnostic features of the validated instruments.

### Population & Limitations
Data was collected from Bangladeshi university students aged primarily 18–25. This introduces a **demographic scope limitation**: the model is trained on a young, academically active population, which may not generalize to older adults or non-student populations. This limitation is explicitly acknowledged and should be stated in any deployment context.

### Dataset Structure
The dataset contains 39 columns across three groups:

**Demographics (7 columns)**
Age, Gender, University, Department, Academic Year, Current CGPA, Waiver/Scholarship status

**PSS-10 Scale — Stress (12 columns)**
Individual item scores PSS1–PSS10, aggregate Stress Value, Stress Label

**GAD-7 Scale — Anxiety (9 columns)**
Individual item scores GAD1–GAD7, aggregate Anxiety Value, Anxiety Label

**PHQ-9 Scale — Depression (11 columns)**
Individual item scores PHQ1–PHQ9, aggregate Depression Value, Depression Label

### Target Variables
Three separate supervised classification targets — one per condition:
- `Stress Label` → multiclass (Low Stress / Moderate Stress / High Perceived Stress)
- `Anxiety Label` → multiclass (Minimal / Mild / Moderate / Severe Anxiety)
- `Depression Label` → multiclass (No / Mild / Moderate / Moderately Severe / Severe Depression)

---

## ML Pipeline Overview

### 1. Data Profiling
- Class distribution per target variable (check for imbalance)
- Missing value audit
- Severity band mapping confirmation per instrument

### 2. EDA
- Univariate: score distributions per scale (skewness, range)
- Bivariate: inter-scale correlation (Stress ↔ Anxiety ↔ Depression)
- Item-level: variance per PSS/GAD/PHQ item (identify low-signal items)
- Demographic splits: severity by Gender, Academic Year

### 3. Feature Engineering
- Ordinal encode ordered categoricals (CGPA range, Academic Year)
- Binary encode Gender and waiver status
- Handle high-cardinality categoricals (University, Department) via grouping or dropping
- Retain aggregate scale scores as features
- Engineer composite severity flag: `any_moderate_or_above` (referral trigger feature)

### 4. Modelling Strategy
Three separate classifiers — one per condition. Rationale: severity bands differ per instrument; a single multi-label model conflates clinically distinct outputs.

- Baseline: Logistic Regression
- Candidates: Random Forest, XGBoost
- Evaluation metric: Weighted F1 (preferred over accuracy due to class imbalance)
- Validation: Stratified K-Fold cross-validation

---

## System Architecture (High-Level)

```
User Login (FastAPI + Auth)
        ↓
Intake Conversation (LangChain)
        ↓
Screening Engine → PSS-10 / GAD-7 / PHQ-9 administered conversationally
        ↓
ML Classifier → Severity classification per condition
        ↓
Advising Layer → Psychoeducation + Coping strategies
        ↓          (Referral trigger if threshold crossed)
Session Storage → PostgreSQL (scores + history per user)
        ↓
Return Session → Reassessment + trend comparison
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend API | FastAPI |
| Chatbot / Memory | LangChain |
| Database | PostgreSQL |
| ML Models | scikit-learn / XGBoost |
| Language | Python 3.13 |