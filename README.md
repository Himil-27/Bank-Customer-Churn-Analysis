<img width="882" height="187" alt="Screenshot 2026-07-06 144453" src="https://github.com/user-attachments/assets/39d7cb80-a285-4c4b-a8f4-72b4a10e8fda" />


# Bank-Customer-Churn-Analysis
Business focused customer churn analysis using SQL and Power BI with actionable insights, customer segmentation, and retention recommendations.


An end-to-end churn analysis project for a retail bank, built with **SQL Server** (data cleaning & exploration) and **Power BI** (data modeling, DAX measures, and dashboarding).

## Problem Statement

The bank is experiencing customer attrition, leading to potential revenue loss and reduced customer lifetime value. The key drivers of churn and high-risk customer segments were not clearly understood. This project analyzes customer behavior, identifies the primary factors contributing to churn, and delivers actionable, data-driven recommendations to improve retention.

## Objectives

1. Measure and understand churn behavior
2. Identify key drivers of churn
3. Perform multi-dimensional customer segmentation
4. Evaluate the business impact of churn
5. Develop high-risk customer profiles
6. Provide data-driven recommendations

## Project Structure

```
├── bank_customer_churn.csv        # Raw dataset (10,000 rows, 14 columns)
├── 01_data_cleaning.sql            # Data cleaning & preparation script
├── 02_business_analysis.sql         # Exploratory SQL analysis
├── Bank_Screenshots.png            # Power BI dashboard screenshots
└── README.md
```

## Tools & Tech Stack

- **SQL Server** – data cleaning, type casting, deduplication, null checks
- **Power BI (Power Query + DAX)** – conditional columns, calculated measures, data modeling
- **Power BI Desktop** – interactive dashboard with drill-through

## Dataset Overview

10,000 customer records with the following fields:

| Column | Description |
|---|---|
| Customer_Id | Unique customer identifier |
| Surname | Customer surname |
| Credit_Score | Customer credit score |
| Geography | Country (France, Germany, Spain) |
| Gender | Male / Female |
| Age | Customer age |
| Tenure | Years with the bank |
| Balance | Account balance |
| Num_Of_Products | Number of bank products used |
| Has_Credit_Card | Whether the customer holds a credit card |
| Is_Active_Member | Whether the customer is an active member |
| Estimated_Salary | Estimated annual salary |
| Exited (Churn) | Whether the customer churned (1) or not (0) |

## Data Cleaning (SQL)

- Cast `Balance` and `Estimated_Salary` to `FLOAT`
- Checked and confirmed **no duplicate customers** (via `ROW_NUMBER()` partitioned on `Customer_Id`)
- Renamed columns for consistency (e.g., `Has_CC_Yes_No`)
- Verified **no missing/blank values** across all key columns

## Feature Engineering (Power Query)

Custom conditional columns created to enable segmentation:

| New Column | Logic |
|---|---|
| **Age_Group** | Students (18–25), Young Professionals (26–40), Wealth Accumulators (41–60), Retirees (61–75), Senior Citizens (75+) |
| **Activity** | Active / Inactive (from `Is_Active_Member`) |
| **Balance_Group** | No Balance, Low Balance (≤10K), Average Balance (≤50K), High Balance |
| **Tenure_Group** | New (≤2 yrs), Established (≤6 yrs), Loyal (6+ yrs) |
| **Salary_Group** | Low Income (≤7K), Average Income (≤14K), High Income |
| **Credit_Score_Group** | Below Average, Average, Above Average |

## Key DAX Measures

```DAX
Average_Age        = AVERAGE(Bank_Churn[Age])
Customer_cnt        = COUNT(Bank_Churn[Customer_Id])
Churner_cnt          = CALCULATE([Customer_cnt], Bank_Churn[Exited] = 1)
Non_Churner_cnt      = CALCULATE(COUNT(Bank_Churn[Customer_Id]), Bank_Churn[Exited] = 0)
Churn_Contribution   = DIVIDE([Churner_cnt], 10000) * 100
Churn_Rate           = DIVIDE([Churner_cnt], [Customer_cnt]) * 100
Non_Churn_Rate       = DIVIDE([Non_Churner_cnt], [Customer_cnt]) * 100
```

## Dashboard Pages (Power BI)

1. **Customer Demographics** – overall customer count, churner count, breakdown by gender, geography, credit card ownership, and churn
2. **Strong Insights** – churn rate & contribution by Activity, Number of Products, Age Group, Geography, and Balance Group
3. **Weak Insights** – churn rate & contribution by Salary Group and Credit Score Group (low signal, included for completeness)
4. **Combination Analysis** – churn rate across combined dimensions (e.g., Age Group × Activity, Balance Group × Activity, Age Group × Num_Of_Products)

**Overall churn rate: 20.37%** (2,037 churners out of 10,000 customers)

## Key Findings

### Strong Insights

| Driver | Finding |
|---|---|
| **Activity** | Inactive customers churn at ~27% — the #1 driver of churn |
| **Number of Products** | 1 product → 28% churn vs. 2 products → 8% churn |
| **Age Group** | Wealth Accumulators (41–60) churn at ~40% vs. Young Professionals (26–40) at ~11% |
| **Geography** | Germany churns at ~32% vs. France at ~16% |
| **Balance Group** | High-balance customers churn more than no-balance customers (24% vs. 14%) — a direct revenue risk |

### Weak Insights

- **Salary Group** — 93% of customers fall into one "High Income" bucket, making the segmentation too imbalanced to be useful
- **Credit Score** — churn rates range only 19–21% across bands, indicating it is not a strong standalone predictor

### Combination Insights

- Inactive customers with only **1 product** show the highest churn (up to ~37%)
- **Inactive customers aged 41–60** show the single highest churn risk (~51%)
- Moving customers from 1 → 2 products drastically reduces churn across nearly every segment
- High-balance customers are not immune to churn when inactive or under-engaged with products

## High-Risk Customer Personas

| Persona | Profile | Risk |
|---|---|---|
| Disengaged Mid-Age Customer | Age 41–60, Inactive, 1 product, High balance | Highest risk |
| Single-Product Inactive User | Any age, Inactive, 1 product | High volume + high churn |
| Young but Under-engaged | Age 25–40, Inactive, 1 product | Lower risk, scalable impact |

## Business Recommendations

1. **Cross-Sell Strategy** – Push customers from 1 → 2 products, prioritizing inactive users
2. **Engagement Campaigns** – Target inactive users, especially in the 41–60 age group
3. **High-Value Customer Retention** – Offer dedicated support and personalized offers to high-balance customers
4. **Lifecycle-Based Strategy** – Tailor retention approaches separately for young professionals vs. wealth accumulators

## How to Reproduce

1. Import `bank_customer_churn.csv` into SQL Server and run `01_data_cleaning.sql` to prepare the data
2. Run `02_business_analysis.sql` for exploratory analysis
3. Load the cleaned table into Power BI and apply the conditional columns described above
4. Add the DAX measures listed above
5. Rebuild the dashboard pages: Customer Demographics, Strong Insights, Weak Insights, Combination Analysis

