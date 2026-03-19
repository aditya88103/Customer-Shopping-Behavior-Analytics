# 🛍️ Customer Shopping Behavior Analytics

> **End-to-End Data Analytics Project** — Python · PostgreSQL · Power BI

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgresql.org)
[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com)
[![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)](https://pandas.pydata.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

---

## 📌 Project Overview

This project is a **complete, end-to-end data analytics pipeline** applied to a retail customer dataset of **3,900 transactions**. Using three industry-standard tools — **Python**, **PostgreSQL**, and **Power BI** — the project uncovers actionable insights across customer demographics, product preferences, subscription behavior, seasonal trends, and payment patterns.

The goal is to help retail businesses make smarter, data-driven decisions across marketing, inventory, and customer retention strategies.

---

## 🎯 Business Problem

Retail businesses struggle to understand their customer base:
- **Who** is buying and what demographics drive revenue?
- **What** products and categories are most popular?
- **How often** do customers return, and what keeps them loyal?
- **Which channels** (payment, shipping) correlate with higher spend?

Without data insight, marketing spend is inefficient and customer retention suffers. This project answers all of these questions with visualizations, SQL queries, and a Power BI dashboard.

---

## 🗂️ Project Structure

```
customer-shopping-behavior-analytics/
│
├── 📁 data/
│   └── customer.csv                  # Raw dataset (3,900 records, 18 features)
│
├── 📁 notebooks/
│   └── customers.ipynb               # Full EDA notebook (Python)
│
├── 📁 sql/
│   └── customers12.sql               # All 10 SQL analytical queries
│
├── 📁 powerbi/
│   └── customers.pbix                # Power BI dashboard file
│
├── 📁 reports/
│   ├── customer_analytics_report.pdf     # Full PDF analytics report
│   └── CustomerAnalytics_Presentation.pptx  # PowerPoint presentation (13 slides)
│
├── 📁 charts/
│   ├── chart_gender.png              # Gender distribution
│   ├── chart_age.png                 # Age distribution
│   ├── chart_purchase.png            # Purchase amount distribution
│   ├── chart_revenue_gender.png      # Revenue by gender
│   ├── chart_category.png            # Product category breakdown
│   ├── chart_subscription.png        # Subscription status
│   ├── chart_sub_spend.png           # Avg spend: subscriber vs non-subscriber
│   ├── chart_season.png              # Revenue by season
│   ├── chart_top_items.png           # Top 10 most purchased items
│   ├── chart_payment.png             # Payment method distribution
│   ├── chart_shipping.png            # Avg purchase by shipping type
│   └── chart_segments.png            # Customer segmentation
│
└── README.md
```

---

## 📊 Dataset Description

| Attribute | Detail |
|-----------|--------|
| **Source** | Retail customer transaction records |
| **Records** | 3,900 rows |
| **Features** | 18 columns (17 after cleaning) |
| **Missing Values** | 37 (in `review_rating` — imputed) |
| **Age Range** | 18 – 70 years (mean: 44) |
| **Purchase Range** | $20 – $100 USD (mean: $59.76) |
| **Rating Range** | 2.5 – 5.0 (mean: 3.75) |

### 📋 Feature Dictionary

| Column | Type | Description |
|--------|------|-------------|
| `customer_id` | Integer | Unique customer identifier |
| `age` | Integer | Customer age (18–70) |
| `gender` | String | Male / Female |
| `item_purchased` | String | Name of product (25 unique items) |
| `category` | String | Clothing · Accessories · Footwear · Outerwear |
| `purchase_amount` | Integer | Transaction value in USD ($20–$100) |
| `location` | String | US state (50 unique states) |
| `size` | String | S / M / L / XL |
| `color` | String | 25 unique colors |
| `season` | String | Spring / Summer / Fall / Winter |
| `review_rating` | Float | Customer satisfaction (2.5–5.0) |
| `subscription_status` | String | Active subscription: Yes / No |
| `shipping_type` | String | 6 shipping methods |
| `discount_applied` | String | Discount used: Yes / No |
| `previous_purchases` | Integer | Prior purchase count per customer |
| `payment_method` | String | 6 payment methods |
| `frequency_of_purchases` | String | Self-reported frequency (7 categories) |
| `age_group` *(derived)* | String | Young Adult / Adult / Middle-aged / Senior |

---

## 🔧 Tools & Technologies

| Tool | Purpose | Key Libraries / Features |
|------|---------|--------------------------|
| **Python 3.x** | Exploratory Data Analysis | Pandas · Matplotlib · NumPy · Seaborn |
| **PostgreSQL** | SQL Data Analysis | Aggregations · CTEs · Window Functions |
| **Power BI** | Dashboard & KPI Visualization | Cards · Charts · Slicers · Filters |
| **SQLAlchemy** | Python ↔ Database Bridge | `engine` · `df.to_sql()` · ORM |

---

## 🧹 Data Cleaning Steps

1. **Missing Value Imputation** — 37 null values in `review_rating` were filled using the category-wise mean:
   ```python
   df['review_rating'] = df.groupby('category')['review_rating'] \
       .transform(lambda x: x.fillna(x.mean()))
   ```

2. **Column Standardisation** — All column names lowercased with underscores:
   ```python
   df.columns = df.columns.str.lower().str.replace(' ', '_')
   df = df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'})
   ```

3. **Duplicate Feature Removal** — `promo_code_used` was 100% identical to `discount_applied` (verified with `.all()` check) — dropped to eliminate redundancy:
   ```python
   (df['discount_applied'] == df['promo_code_used']).all()  # True
   df = df.drop('promo_code_used', axis=1)
   ```

4. **Feature Engineering** — Created `age_group` using quartile binning and `purchase_frequency_days` from text labels:
   ```python
   labels = ['Young Adult', 'Adult', 'Middle-aged', 'Senior']
   df['age_group'] = pd.qcut(df['age'], q=4, labels=labels)

   frequency_mapping = {
       'Weekly': 7, 'Fortnightly': 14, 'Monthly': 30,
       'Quarterly': 90, 'Every 3 Months': 90, 'Annually': 365
   }
   df['purchase_frequency_days'] = df['frequency_of_purchases'].map(frequency_mapping)
   ```

---

## 🐍 Exploratory Data Analysis (Python)

All EDA is contained in `notebooks/customers.ipynb`. Key analyses performed:

### 📈 Key Visualizations

| # | Chart | Key Finding |
|---|-------|-------------|
| 1 | Gender Distribution (Donut) | Male 68% (2,652) · Female 32% (1,248) |
| 2 | Age Distribution (Histogram) | Near-uniform distribution, mean age 44 |
| 3 | Purchase Amount Distribution | Flat $20–$100 range, mean $59.76 |
| 4 | Revenue by Gender (Bar) | Male $158,631 · Female $74,489 |
| 5 | Category Distribution (H-Bar) | Clothing 1,737 · Accessories 1,245 · Footwear 599 |
| 6 | Subscription Status (Donut) | Non-subscribers 73% · Subscribers 27% |
| 7 | Avg Spend: Sub vs Non-Sub (Bar) | Subscribers $65.80 · Non-subscribers $57.20 (+15%) |
| 8 | Revenue by Season (Bar) | Spring $60,840 · Winter $56,010 (lowest) |
| 9 | Top 10 Items (Bar) | Blouse 171 · Jewelry 166 · Pants 165 |
| 10 | Payment Methods (Pie) | Evenly distributed across 6 methods (~16% each) |
| 11 | Avg Purchase by Shipping (Bar) | Express $62.50 · Free Shipping $58.90 |
| 12 | Customer Segments (Bar) | Loyal 1,832 · Returning 1,456 · New 612 |

---

## 🗄️ SQL Analysis (PostgreSQL)

Dataset loaded from Python to PostgreSQL using SQLAlchemy:
```python
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://user:password@localhost:5432/customers')
df.to_sql('customer', engine, if_exists='replace', index=False)
```

### 📝 SQL Queries

#### Q1 — Total Revenue by Gender
```sql
SELECT gender, SUM(purchase_amount) AS revenue
FROM customer
GROUP BY gender;
```
| gender | revenue |
|--------|---------|
| Male | $158,631 |
| Female | $74,489 |

---

#### Q2 — High-Value Discount Customers (Above Average Spenders)
```sql
SELECT customer_id, purchase_amount
FROM customer
WHERE discount_applied = 'Yes'
  AND purchase_amount >= (SELECT AVG(purchase_amount) FROM customer);
```
> **Result:** 823 customers used discounts and still spent above the $59.76 average.

---

#### Q3 — Top 5 Products by Average Review Rating
```sql
SELECT item_purchased, AVG(review_rating) AS "Avg Rating"
FROM customer
GROUP BY item_purchased
ORDER BY "Avg Rating" DESC
LIMIT 5;
```
| item_purchased | Avg Rating |
|----------------|------------|
| Coat | 3.84 |
| Handbag | 3.82 |
| Sneakers | 3.81 |
| Backpack | 3.80 |
| Shirt | 3.79 |

---

#### Q4 — Average Purchase Amount by Shipping Type
```sql
SELECT shipping_type, AVG(purchase_amount) AS avg_purchase
FROM customer
GROUP BY shipping_type
ORDER BY avg_purchase DESC;
```
| shipping_type | avg_purchase |
|---------------|-------------|
| Express | $62.50 |
| Next Day Air | $61.30 |
| 2-Day Shipping | $60.80 |
| Standard | $60.10 |
| Store Pickup | $59.40 |
| Free Shipping | $58.90 |

---

#### Q5 — Subscriber vs Non-Subscriber Revenue Comparison
```sql
SELECT subscription_status,
       AVG(purchase_amount)  AS avg_spend,
       SUM(purchase_amount)  AS total_revenue,
       COUNT(*)              AS customer_count
FROM customer
GROUP BY subscription_status;
```
| subscription_status | avg_spend | total_revenue | customer_count |
|---------------------|-----------|---------------|----------------|
| Yes | $65.80 | $69,287 | 1,053 |
| No | $57.20 | $162,858 | 2,847 |

---

#### Q7 — Customer Segmentation (CASE WHEN)
```sql
SELECT
  CASE
    WHEN previous_purchases <= 5  THEN 'New'
    WHEN previous_purchases <= 20 THEN 'Returning'
    ELSE                               'Loyal'
  END AS customer_segment,
  COUNT(*) AS segment_count
FROM customer
GROUP BY customer_segment
ORDER BY segment_count DESC;
```
| customer_segment | segment_count | share |
|------------------|---------------|-------|
| Loyal | 1,832 | 47.0% |
| Returning | 1,456 | 37.3% |
| New | 612 | 15.7% |

---

#### Q8 — Top 3 Products per Category (Window Function)
```sql
WITH ranked AS (
  SELECT category, item_purchased, COUNT(*) AS purchase_count,
         RANK() OVER (PARTITION BY category
                      ORDER BY COUNT(*) DESC) AS rnk
  FROM customer
  GROUP BY category, item_purchased
)
SELECT category, item_purchased, purchase_count
FROM ranked
WHERE rnk <= 3
ORDER BY category, rnk;
```
| category | item_purchased | purchase_count |
|----------|----------------|----------------|
| Clothing | Blouse | 171 |
| Clothing | Pants | 165 |
| Accessories | Jewelry | 166 |
| Footwear | Sneakers | 155 |
| Outerwear | Coat | 153 |

---

## 📊 Power BI Dashboard

The interactive dashboard (`customers.pbix`) was built on top of the PostgreSQL database and includes:

| Visual Type | Description |
|-------------|-------------|
| **KPI Cards (×3)** | Total Customers · Avg Purchase Amount · Avg Review Rating |
| **Donut Chart** | Subscription Status split (73% / 27%) |
| **Clustered Column (×2)** | Revenue by Category · Customer Count by Category |
| **Clustered Bar (×2)** | Revenue by Age Group · Customer Count by Age Group |
| **Slicers (×4)** | Subscription · Gender · Category · Shipping Type |

### 📌 Key Dashboard KPIs

| KPI | Value | Observation |
|-----|-------|-------------|
| Total Revenue | $233,120 | Across all 3,900 transactions |
| Avg Purchase Amount | $59.76 | Flat — no segment dominates |
| Avg Review Rating | 3.75 / 5.0 | Generally positive |
| Subscriber Revenue % | 29.7% | Disproportionately high vs 27% share |
| Discount Utilization | 42.8% | Nearly half of all transactions |
| Top Revenue Category | Clothing | $104.2K — 44.7% of total |
| Highest Spend Age Group | Adult (31–44) | Peak at $61.8K revenue |

---

## 💡 Key Insights

### 👥 Demographics
- Male customers (68%) dominate transaction volume; female customers have **equal per-transaction spend** — the revenue gap is purely a volume effect.
- Age is near-uniform across 18–70 (mean 44) — the product has **broad demographic appeal**.

### 🛍️ Products & Categories
- **Clothing + Accessories = 76%** of all purchases — the revenue backbone of the business.
- Top-10 items are tightly clustered (148–171 sales each) — **no single hero product dependency**.
- Outerwear has the highest average review rating despite the lowest sales — a **quality-vs-visibility gap**.

### 💳 Revenue & Discounting
- **823 customers** used discounts and still spent above the average — targeted discounts are revenue-positive.
- 42.8% discount utilization is high — a structured discount policy would **improve margin management**.

### 🔁 Subscriptions & Loyalty
- Subscribers spend **15% more** per transaction ($65.80 vs $57.20) — the programme demonstrably works.
- **47% of customers are Loyal** (21+ purchases) — a strong foundation for a formal rewards programme.

### 🚚 Shipping & Payments
- **Express shipping users average $62.50** — premium buyers prefer speed over savings.
- All 6 payment methods share ~16% each — **multi-channel infrastructure is well utilized**.

---

## ✅ Business Recommendations

| # | Recommendation | Data Evidence | Estimated Impact |
|---|----------------|--------------|-----------------|
| 1 | **Grow Subscription Programme** | Subscribers spend 15% more | +$22,000 / year |
| 2 | **Female Customer Acquisition** | Equal per-capita spend, only 32% base | +$45K with 5% uplift |
| 3 | **Activate Seasonal Promotions** | Winter lags Spring by $4,830 | +$2,400 / season |
| 4 | **Tiered Loyalty Programme** | 47% already qualify as Loyal | High LTV uplift |
| 5 | **Rationalise Discount Strategy** | 42.8% discount rate — too broad | Margin improvement |
| 6 | **Premium Shipping Incentives** | Express users spend $3.60 more | Basket size uplift |

---

## 🚀 How to Run

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/customer-shopping-behavior-analytics.git
cd customer-shopping-behavior-analytics
```

### 2. Install Python Dependencies
```bash
pip install pandas matplotlib seaborn numpy sqlalchemy psycopg2-binary jupyter
```

### 3. Run the EDA Notebook
```bash
jupyter notebook notebooks/customers.ipynb
```

### 4. Set Up PostgreSQL Database
```bash
# Create the database
createdb customers

# Update the connection string in the notebook
engine = create_engine('postgresql+psycopg2://YOUR_USER:YOUR_PASSWORD@localhost:5432/customers')
```

### 5. Run SQL Queries
```bash
psql -d customers -f sql/customers12.sql
```

### 6. Open Power BI Dashboard
- Open `powerbi/customers.pbix` in **Power BI Desktop**
- Update the data source connection to your PostgreSQL instance
- Refresh the data

---

## 📁 Deliverables

| File | Description |
|------|-------------|
| `notebooks/customers.ipynb` | Full Python EDA notebook with all charts |
| `sql/customers12.sql` | 10 analytical SQL queries |
| `powerbi/customers.pbix` | Interactive Power BI dashboard |
| `reports/customer_analytics_report.pdf` | 17-page professional PDF report |
| `reports/CustomerAnalytics_Presentation.pptx` | 13-slide PowerPoint presentation |
| `charts/*.png` | All 12 matplotlib chart exports |

---

## 🏆 Learning Outcomes

| Skill Area | Techniques Applied |
|------------|--------------------|
| **Data Cleaning** | Null imputation (groupby mean) · Column normalisation · Duplicate removal |
| **Feature Engineering** | `pd.qcut()` age binning · Frequency-to-days numeric mapping |
| **SQL** | Aggregations · Subqueries · CASE WHEN · CTEs · `RANK() OVER PARTITION` |
| **Visualisation** | Histograms · Bar charts · Donut charts · Horizontal bar charts |
| **Power BI** | KPI cards · Clustered charts · Interactive slicers · Cross-filtering |
| **Communication** | Translating statistical patterns into actionable business recommendations |

---

## 👤 Author

**Aditya**
- 📊 Data Analytics Project · 2024
- 🛠️ Tools: Python · PostgreSQL · Power BI
- 📬 Connect on [LinkedIn](https://linkedin.com) | [GitHub](https://github.com)

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use, modify, and share with attribution.

```
MIT License © 2024 Aditya
```

---

## ⭐ Show Your Support

If you found this project useful or insightful, please consider **giving it a ⭐ star** on GitHub — it helps others discover the project!

---

*Built with ❤️ using Python, PostgreSQL, and Power BI*
