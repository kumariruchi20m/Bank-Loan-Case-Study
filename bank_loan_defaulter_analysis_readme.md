# 🏦 Bank Loan Defaulter Risk Analysis & EDA Case Study

An exploratory data analysis (EDA) and credit risk assessment on loan application data. This project analyzes customer demographic, financial, and behavioral attributes to identify underlying patterns that distinguish non-defaulters (`TARGET = 0`) from clients experiencing payment difficulties (`TARGET = 1`).

## 📌 Table of Contents

* [Business Problem & Objective](#-business-problem--objective)

* [Data Overview & Cleaning](#-data-overview--cleaning)

* [Class Imbalance](#-class-imbalance)

* [Exploratory Data Analysis & Segmentations](#-exploratory-data-analysis--segmentations)

  * [Application Timing Patterns](#1-application-timing-patterns)

  * [Contract Type Preferences](#2-contract-type-preferences)

  * [Housing Type Distribution](#3-housing-type-distribution)

  * [Education vs. Default Propensity](#4-education-vs-default-propensity)

* [Key Correlations & Metric Relationships](#-key-correlations--metric-relationships)

* [Risk Factors & Strategic Recommendations](#-risk-factors--strategic-recommendations)

* [Repository Layout](#-repository-layout)

* [Tools & Methodology](#-tools--methodology)

## 🎯 Business Problem & Objective

When assessing loan applications, lending institutions encounter two distinct failure modes:

1. **False Positives (Rejection of Creditworthy Clients):** Rejecting loans for clients who would successfully repay leads to lost business opportunity.

2. **False Negatives (Approval of Defaulters):** Approving credit to clients prone to non-payment results in direct financial loss and elevated Non-Performing Assets (NPAs).

**Primary Objective:**

Utilize Exploratory Data Analysis (EDA) on client applications (`application_data`) and historical loan records (`previous_application`) to pinpoint high-risk customer segments, isolate leading default indicators, and provide data-driven underwriting criteria.

## 🧹 Data Overview & Cleaning

The dataset is partitioned into primary application records and historical loan histories:

| Data Asset | Description | 
| ----- | ----- | 
| **`application_data`** | Demographics, income, family status, loan amount, and repayment target flag. | 
| **`previous_application`** | Historical loan attempts, previous approval/rejection status, and credit histories. | 
| **`columns_description`** | Data dictionary detailing attribute semantics and measurement scales. | 

### Data Preparation Steps

* **Missing Value Imputation:** Inspected missing values across financial and demographic attributes. Applied median imputation for heavily skewed numerical values (e.g., `AMT_ANNUITY`, `AMT_GOODS_PRICE`) and mode for categorical fields.

* **Outlier Capping:** Analyzed skewed financial variables such as `AMT_CREDIT` using box plots and whisker thresholds to isolate extreme values from standard customer behavior.

* **Feature Segregation:** Split customer cohorts into target partitions:

  * **Non-Defaulters (`TARGET = 0`):** Clients who repaid loans on time.

  * **Defaulters (`TARGET = 1`):** Clients with recorded payment difficulties (late payment $> X$ days).

## ⚖️ Class Imbalance

The distribution of the target variable reveals significant credit skew:

```
+-----------------------+------------+-------------+
| Class / Target        | Headcount  | Percentage  |
+-----------------------+------------+-------------+
| Non-Defaulters (0)    | 282,686    | 91.93%      |
| Defaulters (1)        |  24,825    |  8.07%      |
+-----------------------+------------+-------------+
| Total Records         | 307,511    | 100.00%     |
+-----------------------+------------+-------------+

```

$$
\text{Imbalance Ratio} \approx 91.93 : 8.07 \quad (\approx 11.4 : 1)
$$

> **Risk Modeling Note:** In credit underwriting, an 8% default rate represents substantial portfolio risk. Predictive models must account for this imbalance via class weighting, SMOTE, or recall/precision optimization.

## 📊 Exploratory Data Analysis & Segmentations

### 1. Application Timing Patterns

Evaluating loan application submissions across days of the week (`WEEKDAY_APPR_PROCESS_START`):

| Day of Week | Defaulters (`TARGET = 1`) | Non-Defaulters (`TARGET = 0`) | Combined Volume | 
| ----- | ----- | ----- | ----- | 
| **Sunday** | 1,283 | 14,898 | 16,181 | 
| **Monday** | 3,934 | 46,780 | 50,714 | 
| **Tuesday** | **4,501** | **49,400** | **53,901** (Peak) | 
| **Wednesday** | 4,238 | 47,696 | 51,934 | 
| **Thursday** | 4,098 | 46,493 | 50,591 | 
| **Friday** | 4,101 | 46,237 | 50,338 | 
| **Saturday** | 2,670 | 31,182 | 33,852 | 

* **Observation:** Loan demand is highest on Tuesdays and mid-week, whereas weekend activity drops by over 65%.

### 2. Contract Type Preferences

Comparison between **Cash Loans** and **Revolving Loans**:

| Contract Type | Non-Defaulters (`0`) | Defaulters (`1`) | Total Applications | Default Rate (%) | 
| ----- | ----- | ----- | ----- | ----- | 
| **Cash Loans** | 255,011 | 23,221 | 278,232 | **8.35%** | 
| **Revolving Loans** | 27,675 | 1,604 | 29,279 | **5.48%** | 
| **Total** | **282,686** | **24,825** | **307,511** | **8.07%** | 

* **Observation:** Cash loans make up over 90% of all submitted applications and carry a higher default rate (8.35%) than revolving credit (5.48%).

### 3. Housing Type Distribution

Cross-tabulation of client residential arrangements:

| Housing Type | Target = 0 (Repaid) | Target = 1 (Default) | Total Volume | Segment Default Rate | 
| ----- | ----- | ----- | ----- | ----- | 
| **House / Apartment** | 251,596 | 21,272 | 272,868 | 7.79% | 
| **With Parents** | 13,104 | 1,736 | 14,840 | **11.70%** | 
| **Municipal Apartment** | 10,228 | 955 | 11,183 | 8.54% | 
| **Rented Apartment** | 4,280 | 601 | 4,881 | **12.31%** | 
| **Office Apartment** | 2,445 | 172 | 2,617 | 6.57% | 
| **Co-op Apartment** | 1,033 | 89 | 1,122 | 7.93% | 

* **Observation:** Borrowers living in **Rented Apartments** (12.31%) and **With Parents** (11.70%) demonstrate noticeably higher default probabilities compared to those residing in independent houses or apartments.

### 4. Education vs. Default Propensity

Education segmentation among clients facing payment difficulties (`TARGET = 1`):

| Education Level | Defaulter Count | Percentage Share | 
| ----- | ----- | ----- | 
| **Secondary / Secondary Special** | 19,524 | **78.6%** | 
| **Higher Education** | 4,009 | 16.2% | 
| **Incomplete Higher** | 872 | 3.5% | 
| **Lower Secondary** | 417 | 1.7% | 
| **Academic Degree** | 3 | **< 0.1%** | 
| **Total Defaulters Analyzed** | **24,825** | **100.0%** | 

* **Observation:** Nearly 4 out of 5 defaulters have a Secondary / Secondary Special education level. Conversely, holders of an Academic Degree show near-zero default incidence.

## 📈 Key Correlations & Metric Relationships

Using Pearson correlation analysis (`=CORREL`):

* **Applied Amount vs. Sanctioned Credit:**
  

  $$
  \rho(\text{AMT\_APPLICATION}, \text{AMT\_CREDIT}) = 0.9758
  $$

  * *Interpretation:* Very strong linear relationship. The approved credit closely tracks the customer's initial requested financing amount without major downward haircuts.

* **Client Income vs. Annuity:**
  

  $$
  \rho(\text{AMT\_INCOME\_TOTAL}, \text{AMT\_ANNUITY}) = 0.1917
  $$

  * *Interpretation:* Weak positive correlation. Annuity sizes do not scale strictly with customer income tiers, signaling potential over-leveraging among lower-income brackets.

## 💡 Risk Factors & Strategic Recommendations

### High-Risk Indicators (Red Flags)

1. **Short Employment History:** Clients with **less than 5 years of employment** (`DAYS_EMPLOYED`) exhibit significantly higher default frequencies.

2. **Education Profile:** Borrowers with secondary-level education make up over 78% of delinquent loans.

3. **Living Arrangements:** Rented housing and living with parents carry default rates above 11.5%.

4. **Income Vulnerability:** Borrowers in lower income quintiles taking cash loans experience high debt-to-income stress.

### Underwriting Recommendations

* **Tiered LTV Caps:** Implement stricter Debt-to-Income (DTI) and Loan-to-Value (LTV) limits for applicants in rented accommodations or with shorter tenure (<5 years) in current jobs.

* **Product-Specific Guardrails:** Because cash loans experience higher default volumes, enforce mandatory income verification and debt-service ratio checks for cash products above designated credit limits.

* **Alternative Verification:** For applicants with lower formal education levels, require proof of secondary income streams, guarantors, or collateralization to approve higher credit limits.

## 📁 Repository Layout

```
├── README.md                      # Comprehensive project documentation
├── data/
│   ├── raw/                       # Anonymized application data files
│   └── processed/                 # Cleaned pivot summary tables
├── excel/
│   └── bank_loan_eda_model.xlsx   # Full workbook with formulas, pivots, and charts
├── reports/
│   └── Bank_Loan_Case_Study.pdf   # Presentation deck and findings report
└── LICENSE                        # MIT License

```

## 🛠 Tools & Methodology

* **Primary Tool:** Microsoft Excel 2019 / Office 365

* **Methods:** Pivot Tables & Charts, Data Hygiene (Imputation & Outlier Detection), Univariate & Segmented Analysis, Bivariate Cross-Tabulation, Correlation Analysis (`CORREL`).

## 📜 License

This project is open-source and available under the [MIT License](LICENSE).