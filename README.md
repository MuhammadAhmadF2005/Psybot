# PsyBot: Psychological Assessment & Monitoring Chatbot

PsyBot is an intelligent, memory-enabled chatbot system designed to interact with users, monitor their mental health, maintain structured patient histories, and guide them with personalized advising at each stage of their journey.

---

## Project Scope & Core Ideas

*   **Auth Layer:** Secure registration and user authentication utilizing national identification (NIC / Govt ID).
*   **Screening Layer:** Initial assessment during onboarding to classify the user into specific psychological issue types (or note if no issues are detected).
*   **Memory Layer:** Long-term conversation and history memory powered by LangChain or similar vector/relational databases.
*   **Advising Layer:** Context-aware cross-questioning during active login sessions and real-time evaluation of the user's mental health state.
*   **Reevaluation Layer:** Subsequent login monitoring to compare current metrics with previous session history and track progression/improvement.

---

## Dataset & Sources

PsyBot utilizes a standardized mental health dataset containing university student records.

### Data Source
-Data sourced from https://zenodo.org/records/17389336

### Features Checked
The dataset contains demographics and three standardized mental health metrics:
1.  **Demographics:** Age, Gender, University, Department, Academic Year, Current CGPA, and waiver/scholarship status.
2.  **PSS-10 Scale (Perceived Stress Scale):** Measured through questions `PSS1` to `PSS10`.
3.  **GAD-7 Scale (Generalized Anxiety Disorder):** Measured through questions `GAD1` to `GAD7`.
4.  **PHQ-9 Scale (Patient Health Questionnaire):** Measured through questions `PHQ1` to `PHQ9`.

---
