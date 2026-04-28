# Northstar-Retail-BI-Casestudy
End-to-end BI analysis on a simulated Nigerian retail dataset. Includes data audit, variance analysis, churn impact estimation (₦10.5M LTV at risk), and an executive Power BI dashboard. Built as a structured learning case study.

# 🏪 NorthStar Retail Co. — BI Analysis Case Study
### End-to-End Business Intelligence Project | H1 2024

---

## 📌 Project Overview

This project is a full end-to-end Business Intelligence case study built around a simulated Nigerian retail and e-commerce company — **NorthStar Retail Co.**

The goal was to practice and demonstrate core BI analyst skills:
- Data auditing and quality assessment
- Trend analysis
- Variance analysis
- Customer segmentation
- Churn impact estimation
- Executive dashboard design in Power BI
- Communicating findings to non-technical stakeholders

The project was structured around a realistic business scenario: a General Manager walks in on a Friday afternoon concerned about soft Q2 numbers, margin decline, and a suspicious spike in Electronics discounts in May. The task was to investigate, quantify the damage, and present actionable recommendations before a board meeting.

---

## 🗂️ Dataset Description

The dataset was purpose-built for this case study to simulate real-world retail data — including intentional data quality issues, anomalies, and inconsistencies that a practicing analyst would encounter on the job.

### Files Included

| File | Description |
|---|---|
| `NorthStar_BI_CaseDataset.xlsx` | Main dataset — 4 sheets |
| `NorthStar_Dashboard.pbix` | Power BI dashboard file |
| `README.md` | This documentation file |

### Sheet Structure

#### 1. ⚡ Analyst_Briefing
The starting point for any analyst using this dataset. Contains:
- Full business context and company background
- The GM's problem statement
- Five structured analysis tasks
- Rules of engagement for the analysis

**Read this sheet first before touching any data.**

#### 2. Transaction_Log
Raw transactional data across H1 2024 (January — June).

| Column | Description | Notes |
|---|---|---|
| Transaction_ID | Unique identifier per transaction | Format: TXN00001 |
| Date | Transaction date | Some out-of-range dates exist — dirty data |
| Customer_ID | Unique customer identifier | Links to Customer_Table |
| Customer_Segment | Premium / Regular / New | |
| Category | Product category | 7 categories |
| Product_Name | Product code | Format: CAT-XXXX |
| Channel | Sales channel | Website / Mobile App / Retail Store |
| Region | Customer region | Lagos, Abuja, Port Harcourt, Kano, Ibadan |
| Units_Sold | Number of units per transaction | Some zero values exist — dirty data |
| Unit_Price (₦) | Price per unit in Naira | Some negative values exist — dirty data |
| Revenue (₦) | Total revenue = Units × Price × (1 - Discount) | |
| COGS (₦) | Cost of goods sold | |
| Gross_Profit (₦) | Revenue minus COGS | |
| Discount_% | Discount applied to transaction | Key anomaly column — check May Electronics |
| Payment_Method | Card / Transfer / Cash | |
| Order_Status | Completed / Returned / Cancelled | |
| Delivery_Days | Days to deliver | 0 for cancelled orders |
| Return_Reason | Reason for return if applicable | Has missing values |

**Total rows:** 600 (592 clean, 8 dirty)

#### 3. Customer_Table
Customer master data for 200 unique customers.

| Column | Description | Notes |
|---|---|---|
| Customer_ID | Unique identifier | Links to Transaction_Log |
| Signup_Date | Date customer registered | |
| Segment | Premium / Regular / New | |
| City | Customer city | |
| Age_Group | Age bracket | 18-24, 25-34, 35-44, 45-54, 55+ |
| Total_Orders | Lifetime order count | |
| Total_Spend (₦) | Lifetime spend in Naira | Used for LTV estimation |
| Last_Purchase_Date | Most recent purchase date | Used as churn proxy |
| Loyalty_Points | Accumulated loyalty points | |
| Churn_Flag | 1 = Churned, 0 = Active | Binary flag |

**Total rows:** 200 customers

#### 4. Monthly_Summary
Pre-aggregated monthly performance data for budget vs. actual analysis.

| Column | Description |
|---|---|
| Month | Month label (Jan-24 through Jun-24) |
| Budget_Revenue (₦) | Planned revenue target |
| Actual_Revenue (₦) | Actual revenue achieved |
| Revenue_Variance (₦) | Actual minus Budget |
| Revenue_Var_% | Variance as percentage |
| Budget_GrossProfit (₦) | Planned gross profit |
| Actual_GrossProfit (₦) | Actual gross profit achieved |
| GP_Variance (₦) | GP Actual minus Budget |
| GP_Var_% | GP Variance as percentage |
| New_Customers | New customers acquired that month |
| Returning_Customers | Returning customers that month |
| Churned_Customers | Customers lost that month |
| Return_Volume | Number of product returns |
| Avg_Order_Value (₦) | Average transaction value |

> ⚠️ **Important:** Monthly_Summary figures were pre-aggregated independently. They do not perfectly reconcile with Transaction_Log totals. This is an intentional real-world lesson — always reconcile data sources before presenting findings.

---

## 🧹 Data Cleaning Process

Before any analysis, the Transaction_Log was audited for data quality issues. The following problems were identified and handled:

### Issues Found

| Issue | Records Affected | Action Taken |
|---|---|---|
| Negative Unit_Price values | 5 records | Flagged and excluded from calculations |
| Zero Units_Sold with Completed status | 2 records | Flagged — logically impossible, excluded |
| Date outside analysis period (Dec 2023) | 1 record | Flagged and excluded |
| **Total dirty records** | **8 of 600 (1.3%)** | **Excluded, not deleted** |

### Cleaning Principles Applied

**1. Never delete raw data.**
All 600 original records were preserved. Dirty records were flagged using a `Data_Quality_Flag` column and excluded from calculations using DAX filters — not removed from the source.

**2. Document every exclusion.**
Each exclusion decision was documented with a reason. This creates an audit trail that protects the integrity of the analysis.

**3. Interrogate gaps, not just errors.**
The Return_Reason column had missing values for several returned transactions. This was flagged as a data collection gap — not ignored. Missing data in critical fields affects confidence in findings and was disclosed in the final brief.

### Power Query Steps Applied
- Filtered negative Unit_Price values
- Filtered zero Units_Sold records  
- Filtered transactions with dates before January 2024
- Created Month column using FORMAT function for time intelligence
- Created Month_Sort column for correct chronological ordering

---

## 🔍 Analysis Performed

### Task 1 — Data Audit
Identified and documented 8 dirty records across three error types. Established a clean working dataset of 592 transactions before any analysis began.

### Task 2 — Trend Analysis
Plotted Actual Revenue and Gross Profit across H1 2024. Key finding: revenue peaked in April then declined, but gross profit declined at twice the rate of revenue in May — indicating margin compression beyond simple volume loss.

**Key numbers:**
- April → May revenue decline: **10%**
- April → May gross profit decline: **20.59%**
- Conclusion: Revenue and margin were decoupled in May — a signal of an internal pricing problem, not a market problem.

### Task 3 — Variance Analysis
Investigated the May margin collapse specifically. Filtered Transaction_Log to Electronics transactions in May with discounts above 20%.

**Findings:**
- 7 transactions had discounts between 23% and 49%
- Average Electronics discount in May: **13.6%** vs April: **6.0%**
- Revenue generated at actual discounts: **₦254,054**
- Revenue at normal 7% discount: **₦412,641**
- **Preventable revenue loss: ₦158,587**

### Task 4 — Return Analysis
May return volume was the highest of any month in H1. Returns in May totalled approximately **₦191,718** in reversed revenue. Primary return reasons: defective products and quality issues — consistent with discounted older Electronics inventory being pushed to customers.

**Combined May damage:**

| Issue | Impact |
|---|---|
| Unauthorized discounts | ₦158,587 |
| Product returns | ₦191,718 |
| **Total quantified loss** | **₦350,305** |
| May gross profit gap vs April | ₦418,000 |
| **% of gap explained** | **84%** |

### Task 5 — Churn Analysis
Identified a strong correlation between return volume and churned customers across H1 2024.

- **Return–Churn correlation: r = 0.79**
- Customers churned in May and June: **45**
- Segment distribution: Premium 24%, Regular 47%, New 29%

**Lifetime Value at Risk Estimation:**

| Segment | Churned (est.) | Avg Spend | LTV at Risk |
|---|---|---|---|
| New (29%) | 13 | ₦69,000 | ₦897,000 |
| Regular (47%) | 21 | ₦210,700 | ₦4,424,700 |
| Premium (24%) | 11 | ₦475,600 | ₦5,231,600 |
| **Total** | **45** | | **~₦10.5M** |

---

## 📊 Dashboard — Power BI

The final deliverable was an interactive executive dashboard built in Power BI using a custom dark theme.

### Design System

| Element | Value |
|---|---|
| Canvas background | `#1E1E2E` |
| Panel background | `#2A2A3E` |
| Primary accent | `#C9A84C` Gold |
| Alert colour | `#E05C5C` Coral |
| Secondary accent | `#4ECDC4` Teal |
| Chart colour 2 | `#4A6FA5` Blue |
| Primary text | `#FFFFFF` |
| Secondary text | `#A0A0B0` |

### Dashboard Sections

**KPI Row — 6 cards:**
- Total Revenue
- Total Gross Profit
- GP Margin %
- Average Discount %
- Total Returns
- Monthly Churn

**Row 2 — Primary Charts:**
- Revenue vs GP Margin % dual axis line chart (hero visual — tells the May story)
- Revenue by Category horizontal bar chart with margin % tooltips

**Row 3 — Supporting Charts:**
- Returns vs Churn monthly trend line chart
- Revenue by Region horizontal bar chart
- May Anomaly — Action Required callout panel

**Interactive Slicers:**
- Month (dropdown)
- Category (dropdown)
- Region (dropdown)

### DAX Measures Created

```
Total Revenue = SUM(Transaction_Log[Revenue (₦)])

Total GP = SUM(Transaction_Log[Gross_Profit (₦)])

GP Margin % = DIVIDE([Total GP], [Total Revenue])

Avg Discount % = AVERAGE(Transaction_Log[Discount_%])

Monthly Returns = CALCULATE(
    COUNT(Transaction_Log[Transaction_ID]),
    Transaction_Log[Order_Status] = "Returned"
)

Churned Customers = SUM(Customer_Table[Churn_Flag])

Monthly Churn = CALCULATE(
    SUM(Customer_Table[Churn_Flag]),
    USERELATIONSHIP(
        Customer_Table[Last_Purchase_Date],
        Transaction_Log[Date]
    )
)

Gross Margin % by Category = DIVIDE(
    SUM(Transaction_Log[Gross_Profit (₦)]),
    SUM(Transaction_Log[Revenue (₦)])
)
```

### Data Model Relationships

```
Transaction_Log ──(Many-to-One)──► Customer_Table
     │                              [Customer_ID]
     │
     └──(Many-to-One)──► Monthly_Summary
                          [Month column]

Transaction_Log ··(Inactive)··► Customer_Table
                                [Date ↔ Last_Purchase_Date]
                                Used via USERELATIONSHIP in Monthly Churn measure
```

---

## 📋 Executive Brief Summary

**Problem:**
Q2 profitability declined sharply despite reasonable revenue performance. May was the critical inflection point — margins collapsed, returns spiked, and customer churn accelerated.

**Key Findings:**
1. 7 Electronics transactions in May carried discounts between 23–49%, causing ₦158,587 in preventable revenue loss
2. May return volume was the highest in H1, reversing ₦191,718 in revenue — driven by defective and poor quality products
3. These two issues together account for 84% of May's ₦418,000 gross profit gap versus April
4. Churned customers in May–June represent approximately ₦10.5M in lifetime value at risk, with a 0.79 correlation between returns and churn

**Recommendations:**
1. Audit and lock discount authorization — no Electronics discount above 15% without GM sign-off
2. Implement quality checkpoint for clearance inventory before dispatch
3. Launch targeted re-engagement campaign for 45 churned customers — break-even requires only 3 customers to return

---

## ⚠️ Data Limitations & Assumptions

- 8 records excluded due to data quality issues (1.3% of dataset)
- Return_Reason field has missing values — full return attribution not possible
- Monthly_Summary and Transaction_Log do not perfectly reconcile — Transaction_Log used as source of truth for all live measures
- LTV at risk figure is an estimate based on segment averages — actual figure depends on individual customer behaviour
- Churn flag is static — does not capture exact date of churn precisely

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Microsoft Excel | Dataset storage and initial review |
| Power BI Desktop | Data modelling, DAX measures, dashboard |
| Power Query | Data cleaning and transformation |
| DAX | Calculated measures and time intelligence |

---

## 👤 Author

**Chidera Patrick Anajekwu**

BI Analyst | Data Analyst | Statistician

> *"The goal of a BI Analyst is not to produce dashboards — it is to reduce uncertainty for decision makers."*

---

## 📁 How to Use This Dataset

1. Download the Excel file
2. Read the **⚡ Analyst_Briefing** sheet first — understand the business context before opening any data
3. Audit the **Transaction_Log** for dirty records before any analysis
4. Complete the 5 analysis tasks in the briefing sheet
5. Build your own dashboard and executive brief
6. Compare your findings against this documentation

**Suggested tools:** Power BI, Excel, Python (Pandas), Tableau, SQL

---

*592 validated transactions · 200 customers · H1 2024 · NorthStar Retail Co. (Simulated Dataset)*
