# 01. Business Problem

**Project:** E-Commerce Intelligence: Operations & Customer Experience Analytics
**Dataset:** Brazilian E-Commerce Public Dataset by Olist (Kaggle)
**Role framing:** Data Analyst supporting the Operations and Seller Management teams

---

## 1. Executive Summary

Olist is a Brazilian marketplace that connects small and medium sellers to customers. Olist does not pack or ship most products itself, because sellers and logistics carriers do. Yet when delivery is late or the product disappoints, the customer's frustration lands on the platform, in the form of low review scores, lost repeat purchases, and a weaker brand.

This project analyzes roughly 100,000 orders (2016-2018) to answer one central question:

> **How do delivery performance and seller quality affect customer satisfaction and revenue, and where should Olist act first?**

---

## 2. Business Context

- Olist operates a **marketplace model**: revenue depends on a healthy network of sellers and on customers who trust the platform enough to buy again.
- Customer experience is shaped by factors Olist only partly controls: seller dispatch speed, carrier delivery time, product quality, and freight cost.
- In e-commerce, **delivery reliability** is one of the strongest drivers of satisfaction. Review scores influence future sales for the seller and the platform.
- Brazil is large and geographically uneven, so delivery performance is likely to differ widely by state and by seller-to-customer route.

---

## 3. Problem Statement

Olist lacks a clear, quantified view of where its operations fall short and what those shortfalls cost.

Specifically, it is unclear:

1. How often orders arrive later than the promised delivery date, and where.
2. Whether late deliveries measurably lower customer review scores.
3. Which sellers, categories, and regions drive poor experiences.
4. How concentrated revenue is, and how exposed Olist is to a few sellers or categories.
5. Whether customers come back after a poor (or good) experience.

Without this evidence, operations and seller-management teams cannot prioritize fixes, and effort is spread thinly instead of aimed at the highest-impact problems.

---

## 4. Objectives

| # | Objective | Outcome |
|---|---|---|
| 1 | Build a reliable, documented data foundation | Medallion pipeline (Bronze, Silver, Gold) with a star schema |
| 2 | Measure core business performance | KPI baseline for revenue, orders, AOV, delivery, and satisfaction |
| 3 | Quantify the link between delivery and satisfaction | Evidence of how delays change review scores |
| 4 | Identify weak spots | Ranked lists of problem sellers, categories, and regions |
| 5 | Understand customer behavior | Repeat purchase rate, RFM segments, cohort retention |
| 6 | Recommend actions | Prioritized, evidence-backed recommendations |

---

## 5. Key Business Questions

### A. Sales and Revenue
- What are total revenue, order volume, and average order value (AOV)?
- How do revenue and orders trend over time? Is there seasonality or event-driven spikes?
- Which categories and states contribute the most revenue?
- How concentrated is revenue (do a few categories or sellers dominate)?

### B. Delivery and Logistics
- What is the average delivery time, and how does it compare with the estimated delivery time?
- What percentage of orders are delivered late?
- Which states and seller-to-customer routes have the worst delays?
- How does freight cost relate to product price and distance?

### C. Customer Satisfaction
- What is the distribution of review scores?
- **Do late deliveries significantly lower review scores, and by how much?**
- Which categories and sellers receive the most low scores?

### D. Seller Performance
- Which sellers generate the most revenue, and which have the highest late rates and lowest scores?
- Is there a group of high-revenue sellers with poor service quality (high risk)?

### E. Customer Behavior
- What share of customers purchase more than once?
- What do RFM segments look like, and where is the biggest opportunity?
- How does retention vary by cohort and by first-order experience?

### F. Payments
- Which payment methods dominate, and how do installments relate to order value?

---

## 6. Hypotheses to Test

| ID | Hypothesis | How it will be tested |
|---|---|---|
| H1 | Late deliveries lead to lower review scores | Compare average scores for on-time vs late orders; statistical test |
| H2 | The longer the delay, the lower the score | Score by delay bucket (on-time, 1-3, 4-7, 8+ days late) |
| H3 | Delivery delays are concentrated in specific states and routes | State and route-level delay analysis |
| H4 | Most customers buy only once, and a bad first experience reduces repeat purchase | Repeat rate and cohort analysis by first-order delivery outcome |
| H5 | A small share of sellers and categories drives most revenue | Pareto analysis |
| H6 | High freight cost relative to price hurts satisfaction | Freight ratio vs review score |

---

## 7. Key Performance Indicators (KPIs)

| KPI | Definition |
|---|---|
| Total Revenue | Sum of item price (and separately, price + freight) for delivered orders |
| Total Orders | Count of distinct orders |
| Average Order Value (AOV) | Total revenue ÷ total orders |
| On-Time Delivery % | Orders delivered on or before the estimated date ÷ delivered orders |
| Average Delivery Time | Mean days from purchase to customer delivery |
| Average Delay (days) | Mean of (delivered date − estimated date) for late orders |
| Average Review Score | Mean review score (1-5) |
| Low-Score Rate | Share of orders with a review score of 1 or 2 |
| Repeat Customer % | Customers with 2+ orders ÷ total unique customers |
| Seller Concentration | Share of revenue from the top 10% of sellers |

> **Data note:** In this dataset, `customer_id` is generated per order, while `customer_unique_id` identifies the actual person. Repeat-customer metrics must use `customer_unique_id`.

---

## 8. Stakeholders and Decisions Supported

| Stakeholder | Interest | Decision this analysis supports |
|---|---|---|
| Operations / Logistics Head | Delivery reliability | Which regions and routes to prioritize for carrier or process improvement |
| Seller Management | Seller quality | Which sellers to coach, monitor, or de-prioritize |
| Customer Experience Team | Satisfaction | Where to focus service recovery and review follow-up |
| Marketing / Growth | Retention | Which customer segments to target with retention campaigns |
| Leadership | Overall health | Whether to invest in logistics, seller quality, or acquisition |

---

## 8.1 Scope

**In scope**
- Order, item, payment, review, customer, seller, product, and geolocation data
- Data cleaning, modeling, EDA, deeper analysis, dashboard, and recommendations

**Out of scope**
- Predictive modeling or machine learning
- Real-time or streaming pipelines
- Text analytics beyond basic review-comment keyword counts
- Financial profitability (the dataset has no cost or margin data)

---

## 9. Approach

| Phase | What happens | Where it lives |
|---|---|---|
| Ingest | Load raw CSVs unchanged into the **Bronze** layer | `notebooks/01_bronze_ingestion.ipynb` |
| Profile | Audit nulls, duplicates, integrity, date logic | `notebooks/02_data_profiling.ipynb` |
| Clean | Standardize and fix data in the **Silver** layer | `notebooks/03_silver_cleaning.ipynb` |
| Enrich | Derive delivery, delay, and customer metrics | `notebooks/04_feature_engineering.ipynb` |
| Model | Build a star schema in the **Gold** layer | `notebooks/05_gold_modeling.ipynb` |
| Explore | Answer the business questions with EDA | `notebooks/06_eda.ipynb` |
| Deep dive | Cohorts, RFM, seller scorecard, statistical tests | `notebooks/07_advanced_analysis.ipynb` |
| Communicate | Power BI dashboard and written recommendations | `dashboard/`, `docs/05_insights_and_recommendations.md` |

**Tools:** Databricks (PySpark, SQL, Delta), SQL Server (optional), Python (Pandas, Matplotlib, Seaborn, Plotly), Power BI (DAX), GitHub.

---



## 10. Assumptions and Limitations

- The data covers roughly **2016-2018** and a single marketplace, so findings may not describe current performance.
- Data is **anonymized**: names, sellers, and some text are masked, and product names are not provided.
- Review comments are in **Portuguese**, so analysis of text is limited.
- There is **no cost or margin data**, so conclusions concern revenue and experience, not profit.
- Not every order has a review, so satisfaction results reflect customers who chose to review (possible response bias).
- Delivery "lateness" is measured against Olist's estimated delivery date, which may itself be conservative or aggressive.
- Correlation between delays and scores does not prove causation. Other factors (product quality, expectations) also affect reviews.

---

## 11. Expected Outcome

By the end of the project, a stakeholder should be able to answer, with data:

- *Where* are deliveries failing, and how badly?
- *How much* does that hurt customer satisfaction and repeat purchase?
- *Which* sellers, categories, and regions should be fixed first?

Findings and recommendations will be documented in [`05_insights_and_recommendations.md`](05_insights_and_recommendations.md).
