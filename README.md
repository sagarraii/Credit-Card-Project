# Credit-Card-Project
this repo holds a detailed view of credit card fraud detection as well as loan defaulter project


## `application_data.csv` — Overview

This file contains **demographic**, **financial**, **employment**, and **credit-related information** about each **loan applicant**. Each row represents one applicant, and the columns contain features that could influence their **creditworthiness**.

---
**`application_data.csv` is a structured dataset containing detailed demographic, financial, and credit-related information about loan applicants, used to predict the likelihood of loan default.**

---

### **Key Columns in `application_data.csv`**

| Column Name              | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| `SK_ID_CURR`             | Unique customer ID                                                          |
| `TARGET`                 | **Target variable**: 1 = customer defaulted, 0 = did not default            |
| `NAME_CONTRACT_TYPE`     | Type of loan (e.g., Cash loans, Revolving loans)                            |
| `CODE_GENDER`            | Gender of applicant                                                         |
| `FLAG_OWN_CAR`           | Does the applicant own a car? (Y/N)                                         |
| `FLAG_OWN_REALTY`        | Does the applicant own a house/property? (Y/N)                              |
| `CNT_CHILDREN`           | Number of children                                                          |
| `AMT_INCOME_TOTAL`       | Total annual income                                                         |
| `AMT_CREDIT`             | Loan amount requested                                                       |
| `AMT_ANNUITY`            | Annuity of the loan (monthly installment)                                   |
| `AMT_GOODS_PRICE`        | Price of the goods for which the loan is being taken                        |
| `NAME_TYPE_SUITE`        | Who accompanied the client when applying                                    |
| `NAME_INCOME_TYPE`       | Income type (e.g., working, state servant, pensioner)                       |
| `NAME_EDUCATION_TYPE`    | Education level                                                              |
| `NAME_FAMILY_STATUS`     | Marital status                                                              |
| `NAME_HOUSING_TYPE`      | Type of housing (e.g., own apartment, rented)                               |
| `DAYS_BIRTH`             | Age in days (negative number)                                               |
| `DAYS_EMPLOYED`          | How many days the applicant has been employed                              |
| `DAYS_REGISTRATION`      | Days since the applicant registered at the current address                  |
| `DAYS_ID_PUBLISH`        | Days since ID was changed                                                   |
| `OCCUPATION_TYPE`        | Applicant's occupation                                                      |
| `CNT_FAM_MEMBERS`        | Number of family members                                                    |
| `EXT_SOURCE_1/2/3`       | External credit scores from other financial institutions                    |
| `FLAG_DOCUMENT_*`        | Flags indicating submission of various documents                            |
| `REGION_RATING_CLIENT`   | Region rating of client location (higher = better)                          |

…and around 122 columns in total.

---

### Target Variable
- `TARGET = 1` → The client **defaulted**
- `TARGET = 0` → The client **did not default**

---

### Importance in Credit Risk
This dataset gives a **snapshot of the applicant at the time of loan application**, making it suitable for:
- **Risk scoring models**
- **Default prediction**
- **Customer segmentation**
---


## `previous_application.csv` — Overview

`previous_application.csv` is a dataset that records the historical loan applications made by customers, including application status, loan amounts, and other related details, which can be used to analyze credit behavior and assess future default risk.

---

### **Description:**
This file contains one row per previous loan application. Customers may appear multiple times if they have applied for more than one loan in the past. It is linked to `application_data.csv` using the `SK_ID_CURR` (customer ID) field.

---

### **Common Columns:**

| Column Name                | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| `SK_ID_CURR`               | Customer ID (foreign key to application_data.csv)                           |
| `SK_ID_PREV`               | Unique ID of the previous application                                       |
| `NAME_CONTRACT_TYPE`       | Type of previous loan (e.g., Cash, Revolving)                               |
| `AMT_APPLICATION`          | Loan amount requested in previous application                              |
| `AMT_CREDIT`               | Credit amount granted in that application                                   |
| `AMT_DOWN_PAYMENT`         | Down payment made                                                           |
| `AMT_ANNUITY`              | Loan annuity for previous application                                       |
| `WEEKDAY_APPR_PROCESS_START` | Day of the week when the application was started                        |
| `HOUR_APPR_PROCESS_START`  | Hour of the day when application was made                                   |
| `NAME_CASH_LOAN_PURPOSE`   | Purpose of the cash loan                                                    |
| `NAME_CONTRACT_STATUS`     | Status of the previous application (e.g., Approved, Refused, Canceled)      |
| `DAYS_DECISION`            | Days relative to current application when decision was made                 |
| `NAME_PAYMENT_TYPE`        | How the payment was made                                                    |
| `CODE_REJECT_REASON`       | Reason for rejection (if any)                                               |
| `RATE_DOWN_PAYMENT`        | Down payment as a percentage of the total                                   |

---

### **Why Two Separate Files?**

1. **Separation of Current vs Historical Data:**
   - `application_data.csv` contains **current loan applications** — this is the data used for making present-day credit decisions.
   - `previous_application.csv` stores **historical applications** — these reflect the applicant’s **past behavior**, which is a strong predictor of future credit risk.

2. **Normalization of Data:**
   - In database terms, this is a **normalized design**. Rather than duplicating past application details in every row of `application_data.csv`, they are kept in a separate file (or table) and linked via `SK_ID_CURR`.
   - This keeps the data structure clean, reduces redundancy, and improves efficiency.

3. **One-to-Many Relationship:**
   - One customer (one row in `application_data.csv`) can have **many previous loans** (many rows in `previous_application.csv`).
   - This structure allows for deeper analysis, such as calculating:
     - Number of previous loans
     - Ratio of approvals to refusals
     - Historical loan amounts, down payments, and behaviors

4. **Flexibility in Analysis:**
   - Analysts and data scientists can decide whether and how to **aggregate** the previous applications data (e.g., using mean, sum, count) before merging it with the current application data for modeling.

5. **Real-World Consistency:**
   - In real banking and credit systems, loan records are managed in separate tables — one for active/current applications and another for past application history — reflecting how systems like CRM or risk engines are architected.

---

### **When to Analyze the Files Separately**

1. **Exploratory Data Analysis (EDA)**
   - Initially, it's ideal to analyze both `application_data.csv` and `previous_application.csv` **separately**.
   - You’ll get a clear understanding of:
     - Structure
     - Data quality
     - Missing values
     - Distribution of variables
     - Types of categorical and numeric features

2. **Understanding Domain-Specific Patterns**
   - Each file has a different semantic meaning:
     - `application_data.csv` shows current demographics and financials
     - `previous_application.csv` shows behavioral patterns over time (e.g., frequency of applications, approval rate)

---

### **When to Merge for Analysis**

1. **Predictive Modeling (e.g., Default Prediction)**
   - You **must merge** them if you want to build a robust model.
   - This lets you incorporate **behavioral insights** (from previous applications) into your prediction features.

2. **Creating Aggregate Features**
   Before merging, you don’t use `previous_application.csv` as-is. You **aggregate** it by `SK_ID_CURR`, for example:
   - Total number of past applications
   - Count of approved vs refused applications
   - Average loan amount requested
   - Most recent application status
   - Average days between past applications

   Then, merge those summary statistics with the main application file.

---

### **Best Practice Approach**

| Phase                  | Suggested Action                                   |
|------------------------|----------------------------------------------------|
| **Initial EDA**        | Analyze files separately                           |
| **Data Cleaning**      | Clean separately (remove outliers, handle nulls)   |
| **Feature Engineering**| Create aggregates in `previous_application.csv`    |
| **Merge**              | Merge **aggregated previous data** with `application_data.csv` using `SK_ID_CURR` |
| **Modeling**           | Use the **merged dataset**                         |

---

### Recommendation
1. **Start separately** to understand each file.
2. **Engineer useful features** from `previous_application.csv`.
3. **Merge thoughtfully** to enhance the feature set for modeling or deeper insight generation.

