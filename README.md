# Colon Cancer Survival Analysis

**Reproducible survival analysis in R comparing overall survival between two colon cancer treatment groups.**

This project demonstrates a complete analytical workflow: from understanding the dataset and defining the correct analysis population to survival estimation, statistical testing, Cox regression, model diagnostics, and interpretation.

**Tools:** R · Quarto · survival  
**Methods:** Kaplan-Meier · Log-Rank Test · Cox Regression · Schoenfeld Residuals

---

## Project Overview

### Research Question

**How does overall survival differ between patients receiving Levamisole and patients receiving Levamisole + 5-Fluorouracil (5-FU)?**

This is a time-to-event problem.

Not every patient experiences the event during follow-up. Some observations are right-censored, meaning that the patient was still alive when observation ended.

For this reason, simply comparing death proportions would ignore important information about follow-up time.

---

## Key Results

| Result | Levamisole | Levamisole + 5-FU |
|---|---:|---:|
| Estimated survival after 1 year | 90.6% | 91.8% |
| Estimated survival after 3 years | 62.9% | 74.3% |
| Estimated survival after 5 years | 53.5% | 63.4% |

At approximately five years, estimated survival was:

**63.4% with Levamisole + 5-FU compared with 53.5% with Levamisole alone.**

This corresponds to an estimated difference of approximately **9.9 percentage points**.

Additional results:

- **Log-rank test:** p = 0.00417
- **Cox hazard ratio:** HR = 0.711
- **95% confidence interval:** 0.562–0.899
- **Proportional-hazards test:** p = 0.737

Under the proportional-hazards assumption, the Cox model estimates an approximately **28.9% lower instantaneous mortality hazard** in the combination group compared with Levamisole alone.

This does not mean 28.9% longer survival or a 28.9 percentage-point improvement in survival.

For this reason, the hazard ratio is interpreted together with the absolute Kaplan-Meier survival estimates.

---

## Analysis Workflow

The project follows a structured analytical process:

```text
Raw colon dataset
        ↓
Understand the event structure
        ↓
Define death as the endpoint
        ↓
Select the two active treatment groups
        ↓
Validate the analysis data
        ↓
Kaplan-Meier survival estimation
        ↓
Log-rank comparison
        ↓
Cox proportional hazards model
        ↓
Check proportional-hazards assumption
        ↓
Interpret results and limitations
```

---

## 1. Data and Analysis Population

The analysis uses the `colon` dataset from the R package `survival`.

The underlying study contains three treatment groups:

- Observation
- Levamisole
- Levamisole + 5-Fluorouracil

For this project, I compare the **two active treatment groups**:

- Levamisole
- Levamisole + 5-FU

### Important Data Structure

The raw dataset contains **two rows per patient**:

- one row for recurrence
- one row for death

Using both rows as independent patients would therefore be incorrect.

Because the research question concerns overall survival, the analysis first selects the death endpoint:

`etype = 2`

The observation group is then excluded because the predefined comparison focuses on the two active treatments.

The final analysis population contains:

**614 patients**

with

**284 observed deaths**

---

## 2. Data Validation

Before fitting statistical models, I check whether the analysis data have the expected structure.

The following checks are performed:

- no missing values in patient ID, treatment group, time or status
- exactly one row per patient
- only the death endpoint is included
- survival times are finite and non-negative
- status contains only valid values
- both intended treatment groups are present

The analysis automatically stops if one of these checks fails.

This distinction is important:

**Data-quality checks do not automatically prove statistical model assumptions.**

For example, technical validation cannot prove that censoring is independent of a patient's prognosis.

---

## 3. Kaplan-Meier Survival Analysis

### Goal

The first analytical question is:

**How does estimated survival develop over time in each treatment group?**

Kaplan-Meier estimation is used because the data contain both observed deaths and right-censored observations.

The method estimates survival without assuming a specific parametric survival distribution.

### Estimated Survival

| Time | Levamisole | Levamisole + 5-FU | Difference |
|---|---:|---:|---:|
| 365 days | 90.6% | 91.8% | +1.2 pp |
| 1,095 days | 62.9% | 74.3% | +11.4 pp |
| 1,825 days | 53.5% | 63.4% | +9.9 pp |

The estimated survival curves are similar early in follow-up.

Later, the survival curve for the **Levamisole + 5-FU** group lies predominantly above the curve for **Levamisole alone**.

The difference is particularly visible after approximately three and five years.

---

## 4. Log-Rank Test

### Goal

Kaplan-Meier curves provide a descriptive comparison.

The next question is:

**Is there evidence that the overall survival distributions differ between the two treatment groups?**

For this purpose, I use a two-sided log-rank test.

### Result

- Chi-square = 8.207
- Degrees of freedom = 1
- p = 0.00417

Under the assumptions of the test, the result provides evidence against identical survival distributions between the two groups.

However, the log-rank test does not provide an estimate of the size of the treatment association.

For that reason, the next step uses a Cox proportional hazards model.

---

## 5. Cox Proportional Hazards Model

### Goal

The Cox model is used to estimate the relative instantaneous mortality hazard between the treatment groups.

The model contains treatment as the only predictor.

Levamisole is used as the reference group.

### Result

**Hazard Ratio = 0.711**

**95% CI = 0.562–0.899**

Under the proportional-hazards assumption:

`1 - 0.711 = 0.289`

This corresponds to an estimated **28.9% lower instantaneous mortality hazard** in the Levamisole + 5-FU group compared with the Levamisole group among patients still alive at a given time.

### Important Interpretation

A hazard ratio of 0.711 does **not** mean:

- 28.9% longer survival
- 28.9 percentage points higher survival
- 28.9% fewer deaths at the end of follow-up

For practical interpretation, the hazard ratio is therefore considered together with the Kaplan-Meier survival estimates.

---

## 6. Model Diagnostics

The Cox model assumes that the relative hazard between the two groups is approximately constant over time.

I assess the proportional-hazards assumption using:

- `cox.zph()`
- scaled Schoenfeld residuals
- graphical diagnostics

### Result

- Chi-square = 0.112
- p = 0.737

The test provides **no statistically detectable evidence against the proportional-hazards assumption**.

However, a large p-value does not prove that the assumption is exactly true.

The graphical diagnostic is therefore interpreted together with the formal test.

---

## How the Methods Work Together

Each method answers a different question.

| Method | Question |
|---|---|
| **Kaplan-Meier** | How does estimated survival develop over time? |
| **Log-rank test** | Do the survival distributions differ? |
| **Cox model** | What is the estimated relative hazard between the groups? |
| **Schoenfeld diagnostics** | Is a constant hazard ratio a reasonable working assumption? |

These methods complement each other, but they use the same underlying observations.

They should therefore not be interpreted as independent confirmations of the result.

---

## Final Interpretation

The descriptive and model-based analyses point in the same direction.

Patients receiving **Levamisole + 5-Fluorouracil** show more favourable estimated overall survival than patients receiving **Levamisole alone** in this dataset.

The most intuitive result is the estimated five-year survival:

- **Levamisole:** 53.5%
- **Levamisole + 5-FU:** 63.4%
- **Difference:** approximately +9.9 percentage points

The Cox model points in the same direction:

**HR = 0.711, 95% CI = 0.562–0.899**

The global survival comparison gives:

**Log-rank p = 0.00417**

Within this historical dataset and under the stated model assumptions, the results therefore indicate more favourable overall survival in the combination group.

---

## Limitations

This project is an exploratory secondary analysis.

Several limitations are important for interpreting the results.

### Independent Censoring

Kaplan-Meier estimation, the log-rank test and the Cox model rely on assumptions concerning censoring.

Technical data checks cannot establish whether censoring is independent of prognosis.

Individual reasons for the end of follow-up could not be fully determined from the sources reviewed.

### Time Origin

The dataset documentation defines `time` as the number of days until an event or censoring.

However, the exact clinical origin of the time scale could not be conclusively established from the sources reviewed.

The analysis therefore uses the time scale provided in the dataset.

### Unadjusted Cox Model

The Cox model includes treatment only.

Variables such as age, sex and other patient characteristics are not included in this first analysis.

### Clinical Interpretation

The analysis is based on a historical dataset.

The results do not by themselves provide a current individual treatment recommendation.

The analysis also does not evaluate:

- adverse effects
- quality of life
- treatment costs

---

## What This Project Demonstrates

This project demonstrates a complete analytical workflow rather than only the execution of individual statistical methods.

### Data Analysis

- understanding a non-standard dataset structure
- identifying the correct unit of analysis
- defining the relevant endpoint
- validating analysis data
- handling right-censored observations

### Statistical Modelling

- Kaplan-Meier estimation
- log-rank testing
- Cox proportional hazards regression
- confidence intervals
- proportional-hazards diagnostics
- Schoenfeld residual analysis

### Analytical Reasoning

- translating a research question into a statistical analysis
- separating descriptive and inferential results
- distinguishing data checks from model assumptions
- interpreting absolute and relative effects correctly
- assessing uncertainty
- identifying limitations before drawing conclusions

### Communication

- translating statistical output into understandable conclusions
- documenting analytical decisions
- communicating uncertainty and limitations
- creating a reproducible Quarto workflow

---

## Reproducibility

The complete analysis is written in **R and Quarto**.

To reproduce the analysis:

1. Clone this repository.

2. Open the project in RStudio or another R environment.

3. Install the required package:

```r
install.packages("survival")
```

4. Render the Quarto document:

```bash
quarto render analyse.qmd
```

The Quarto document contains the complete analysis, code, figures, statistical reasoning, interpretation and software information.

---

## Tech Stack

- **R**
- **Quarto**
- **survival**
- Kaplan-Meier estimation
- Log-rank testing
- Cox proportional hazards regression
- Schoenfeld residual diagnostics
- Statistical reporting and visualization

---

## Data Source

The analysis uses the `colon` dataset distributed with the R package `survival`.

The dataset originates from a historical clinical study investigating adjuvant treatment strategies following colon cancer surgery.

Relevant dataset documentation, publications and methodological sources are documented in the complete Quarto analysis.

---

## Project Purpose

This project was developed as a **learning and portfolio project in applied survival analysis**.

The objective was not simply to run statistical models.

The focus was to build a transparent and reproducible analytical process:

**Research Question → Data Structure → Validation → Statistical Analysis → Model Diagnostics → Interpretation → Limitations**
