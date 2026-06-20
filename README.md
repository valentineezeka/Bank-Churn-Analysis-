# 🏦 Why Are Our Customers Leaving? — A Bank Churn Investigation

![Python](https://img.shields.io/badge/Python-Data%20Analysis-blue?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Wrangling-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-Statistical%20Viz-4c72b0?style=for-the-badge)
![Folium](https://img.shields.io/badge/Folium-Geo%20Mapping-77b829?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

> A Bank Churn Analysis 
> A retention-focused investigation into why bank customers leave, and what the bank should do about it.

---

## 🧭 Executive Summary

*If you only read one section, read this.*

The bank is losing **1 in every 5 customers** — and the losses aren't random.
This analysis of 10,000+ customer records found that churn is concentrated in
three specific, fixable areas:

- 🎯 **Age** — customers over 45 churn at 4–5x the rate of customers under 35
- 🌍 **Geography** — Germany churns at roughly double the rate of France or Spain
- 💤 **Engagement** — inactive members are far more likely to leave than active ones

The bank is also quietly losing some of its **highest-balance customers**,
which is a revenue problem, not just a headcount problem.

---

## ❓ The Questions This Analysis Set Out to Answer

1. How big is the churn problem, really?
2. Is churn concentrated in a particular age group?
3. Is churn evenly spread across countries, or localized?
4. Does customer engagement (active membership) affect the odds of leaving?
5. Is the bank losing low-value customers, or valuable ones?
6. Are any of the dataset's features misleading or redundant?

---

## 🗂️ About the Data

| Attribute   | Details                                       |
|-------------|------------------------------------------------|
| Records     | 10,000 bank customers                          |
| Time span   | Synthetic transaction dates spanning 2020–2025 |
| Countries   | France · Germany · Spain                       |
| Target      | `Exited` — 1 if the customer churned, 0 if retained |

<details>
<summary><strong>📋 Click to expand full data dictionary</strong></summary>

| Column            | Description                          |
|--------------------|---------------------------------------|
| `CustomerId`       | Unique customer identifier            |
| `Surname`          | Customer last name                    |
| `CreditScore`      | Credit score (350–850)                |
| `Geography`        | Country of residence                  |
| `Gender`           | Male / Female                         |
| `Age`              | Customer age                          |
| `Tenure`           | Years with the bank                   |
| `Balance`          | Account balance                       |
| `NumOfProducts`    | Number of bank products held          |
| `HasCrCard`        | Has credit card (1/0)                 |
| `IsActiveMember`   | Active member (1/0)                   |
| `EstimatedSalary`  | Estimated annual salary               |
| `Exited`           | Target — churned (1) or retained (0)  |

</details>

---

## 🧹 Getting the Data Analysis-Ready

Real banking data is never clean. Before any analysis could happen, the
following issues had to be resolved:

| Problem Found | How It Was Fixed |
|---|---|
| `Age` stored with inconsistent type | Converted to nullable integer (`Int64`) |
| Missing `Age` and `Surname` values | `Surname` → filled with `"Unknown"`; `Age` → filled with the dataset mean |
| Duplicate `CustomerId` entries | Removed duplicates, keeping the most recent record |
| Inconsistent `Geography` labels (`'FRA'`, `'French'`, `'France'`) | Standardized all variants to `'France'` |
| Stray `'?'` characters in `Surname` | Stripped from all affected records |
| `HasCrCard` / `IsActiveMember` stored as Yes/No text | Mapped to binary `1` / `0` for analysis |
| No transaction date in the source file | Synthetic dates generated across 2020–2025 to enable time-trend analysis |

This step alone is often where the real "data analyst work" happens — the
charts later in this README only mean something because the data behind them
was untangled first.

---

## 📊 What the Data Showed

### 1. The headline number

A quick statistical pass gave the baseline picture of the bank's customer base:

- **Churn rate:** 20.4% (≈1 in 5 customers)
- **Average age:** low-to-mid 30s/40s across the base
- **Active member rate:** roughly half the customer base is "active"

### 2. Age is the single strongest churn signal

Breaking churn down by age band revealed a sharp, consistent trend:

| Age Group | Churn Rate |
|---|---|
| Under 35 | Below 10% |
| 36–45 | Moderate |
| **46–55** | **50.6%** |
| **56–65** | **48.3%** |

Churn rises steadily with age and **peaks at 46–55**, where roughly half of
all customers in that band eventually leave. This isn't a minor effect — it's
the single clearest pattern in the entire dataset.

### 3. Germany is a geographic outlier

| Country | Churn Rate |
|---|---|
| France | ~16% |
| Spain | ~17% |
| **Germany** | **~32%** |

Germany churns at close to **double** the rate of the other two markets. A
choropleth map (built with `folium`) made this immediately visible — Germany
stands out as a clear hotspot rather than a gradual difference. This kind of
gap usually points to something market-specific: pricing, local competition,
service quality, or onboarding experience in that region.

### 4. Engagement predicts retention better than almost anything else

Customers were split into **active** and **inactive** members and compared:

- Inactive members churn at a **substantially higher rate** than active ones
- This was one of the most visually obvious patterns in the entire analysis

The takeaway: customers who stop engaging with the bank are telling you
they're about to leave, often before they actually do.

### 5. The bank isn't just losing low-value customers

A box plot and violin plot comparing account balances between churned and
retained customers showed that **churned customers tend to carry slightly
higher balances** on average than customers who stayed (correlation of
balance to churn: **+0.118** — weak, but directionally consistent and visible
in the distributions). 

This matters more than the weak correlation number suggests: it means churn
isn't just a volume problem, it's a **revenue protection** problem. The bank
isn't just losing accounts — it's at risk of losing some of its better ones.

### 6. A data quality finding worth flagging

While checking relationships between features, `HasCrCard` and
`IsActiveMember` were found to be **identical for every single customer** in
the cleaned dataset. In a real banking dataset, having a credit card and
being an active member should be two separate, only loosely related facts —
their being perfectly identical here is a sign worth flagging to whoever
owns this data pipeline, since it suggests a possible upstream data issue
rather than a genuine business relationship.

---

## 📈 Visualizations Produced

| Chart | What It Shows |
|---|---|
| Bar chart | Churned customers by geography |
| Line chart | Churn trend, 2020–2025 |
| Pie chart | Overall retained vs. churned split |
| Box plot | Spread of account balances |
| Box plot | Age distribution by churn status |
| Scatter plot | Credit score vs. balance |
| Bar chart | Active vs. inactive member churn rate |
| Correlation heatmap | Relationships between all numeric features |
| Stacked bar chart | Churn % by age group |
| Choropleth map (folium) | Churn intensity by country, on a map of Europe |
| Violin plot | Balance distribution by churn status |

---

## 💡 What the Bank Should Do With This

| Finding | Recommended Action |
|---|---|
| Churn peaks at age 46–65 | Build a dedicated retention offer for mid-to-late-career customers, not just new-customer promotions |
| Germany churns ~2x other markets | Investigate Germany specifically — pricing, service, and local competitors — before assuming the same fix works everywhere |
| Inactive members churn far more | Use inactivity as an early-warning signal; trigger re-engagement outreach before the customer leaves |
| High-balance customers are also leaving | Prioritize retention efforts by account value, not just churn probability |
| `HasCrCard` = `IsActiveMember` for every row | Flag to the data team — this looks like a pipeline issue, not a real pattern |

---

## ⚠️ Limitations & What I'd Do Next

Being upfront about this matters as much as the findings themselves:

- The transaction `Date` column is **synthetic** (randomly generated for this
  exercise), so any time-based trend should be read as a demonstration of
  technique, not a real historical pattern.
- This analysis is **descriptive**, not predictive — it explains *what*
  happened, not a probability score for *who* will churn next. A logical next
  step would be a simple classification model (e.g. logistic regression) to
  turn these patterns into a churn-risk score per customer.
- The dataset, while realistic, is significantly cleaner than most live
  banking data once duplicates and missing values were resolved — real-world
  results would need ongoing data-quality monitoring, not a one-time clean.

---

## 🧰 Tools Used

| Tool | Role in This Project |
|---|---|
| Python | Core language |
| Pandas / NumPy | Data cleaning and aggregation |
| Matplotlib / Seaborn | Statistical visualization |
| Folium | Geographic churn mapping |
| Jupyter / Google Colab | Development environment |

---

## ▶️ Running This Project

```bash
git clone https://github.com/valentineezeka/Bank-Churn-Analysis-.git
cd Bank-Churn-Analysis-
pip install pandas numpy matplotlib seaborn folium openpyxl
jupyter notebook bank_churn_analysis.ipynb
```

Or open `bank_churn_analysis.ipynb` directly on GitHub — all charts and
outputs render inline without needing to run anything.

---

## 📁 Files

| File | Description |
|---|---|
| bank_churn_analysis.ipynb | Full analysis notebook |
| `Bank_Churn_Customer_Data.xlsx` | Cleaned dataset |

---

## 👤 About Me

**Nzubechi Valentine Ezeka**
Data Analytics Graduate — NPower Canada Program, Toronto

[LinkedIn](https://www.linkedin.com/in/nzubechi-ezeka) · [GitHub](https://github.com/valentineezeka)

---
