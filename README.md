# RFM Customer Segmentation Analysis

**Rishabh Jain** — RFM + cohort analysis of ~5,900 customers (UK online retailer, Dec 2009–Dec 2011, ~780K transactions) to find high-value customers, retention risk, and revenue concentration. Python, SQL, Power BI.

## Tools

| Tool | Use |
|---|---|
| **Python** (Pandas, NumPy, Matplotlib) | cleaning, RFM scoring, segmentation, charts |
| **SQL** (SQLite) | segment aggregation, validation, win-back list, Pareto check |
| **Power BI** | interactive dashboard |

## Method
1. Recency (days since last order), Frequency (distinct orders), Monetary (total spend) per customer.
2. Each metric scored 1–5 by quintile (5 = best).
3. R×F combinations mapped to 9 segments (Champions, Loyal, At Risk, Lost, …) on a standard RFM framework.
4. Monthly acquisition cohorts (first-purchase month) measured for repeat purchases over the next 1–12 months.

## Key Insights
- **Revenue is concentrated**: Champions + Loyal = **46% of customers, 83.7% of revenue** (£14.5M / £17.4M) — clear Pareto pattern.
- **Champions alone: 25% of customers, 69% of revenue** (£12M), avg 15.7 orders, last purchase ~20 days ago.
- **£1.09M sits in "At Risk"** — 353 customers, avg 7.6 orders, no purchase in 344 days. Customer 12346 alone: £77.5K spend, 326 days idle — prime win-back target.
- **"Lost" + "Hibernating" = 25% of customers, <3% of revenue** — low retention priority.
- **The leak is month one**: ~21% of a cohort buys again next month, then retention plateaus at 15–22% through month 11. Dec 2009 cohort (955 customers) held 35% at M1 vs ~15–19% for mid-2010 cohorts; revenue retention (~21% at M1) tracks customer retention.

## Dashboard
Power BI Project (`RFM_Dashboard.pbip`) — open text-based format (TMDL semantic model + PBIR report) that Power BI Desktop opens natively and source control tracks.

**Pages**
1. **Overview** — KPI cards (revenue, customers, avg customer value, revenue at risk), segment revenue share, customers by segment, win-back table (At Risk + Cannot Lose Them by spend).
2. **Segments** — customers and revenue per segment, recency vs monetary scatter, cards for Champions + Loyal customer share (46.0%), revenue share (83.7%), At Risk count (353).
3. **Retention Trends** — cohort retention matrix (month × acquisition cohort), average retention curve, M1 retention (21.2%), overall average retention (24.7%).

**Model** — `RFM` + `CohortRetention` tables via Power Query, 13 DAX measures (`Total Revenue`, `Revenue at Risk`, `Total Customers`, `Champions + Loyal Revenue %`, `Champions + Loyal Customers %`, `Month 1 Retention`, `Avg Retention`, …).

Previews (rendered from the report definition against the same CSVs the model loads; the `.pbip` opens as the live report):

**Overview** — ![Overview](dashboard_overview.png)

**Segments** — ![Segments](dashboard_segments.png)

**Retention Trends** — ![Retention Trends](dashboard_retention.png)

## Analysis
**Revenue concentrated in the top two segments** — Champions + Loyal hold 46% of customers and 83.7% of revenue; bottom four segments together under 7%.

![Segment revenue share](chart1_segment_revenue_share.png)

**Broad customer base, narrow revenue** — segment sizes are evenly spread, revenue is not: the Pareto pattern behind the retention recommendation.

![Segment sizes](chart2_segment_sizes.png)

**A small high-value tail drives spend** — most customers cluster at low monetary values with a thin scatter of high-spend Champions; At Risk sits further right on recency (valuable before going quiet).

![Recency vs monetary](chart3_recency_vs_monetary.png)

**Recency and frequency move together** — strongest cells in the high-recency/high-frequency corner (Champions, Loyal); low recency + low frequency maps to Lost and Hibernating.

![Recency vs frequency heatmap](chart4_rf_heatmap.png)

**Cohort retention leaks in month one** — every cohort drops to ~21% by month 2, then plateaus; early Dec 2009 cohort holds materially better than mid-2010 cohorts.

![Cohort retention heatmap](chart5_cohort_retention.png)

## Recommendation
Win back the 353 At Risk customers (£1.09M historical revenue) before they churn, protect Champions with loyalty perks given their outsized revenue share, and add a first-month onboarding offer — ~79% of new customers never make a second purchase.

## Files

| File | Contents |
|---|---|
| `01_Data_Cleaning.ipynb` | drops cancelled orders, missing customer IDs, duplicates |
| `02_rfm_analysis.ipynb` | RFM calculation, scoring, segmentation |
| `03_sql_analysis.ipynb` | SQL validation, win-back list, Pareto check |
| `04_visualization.ipynb` | segment revenue share, recency vs monetary, RF heatmap |
| `05_cohort_analysis.ipynb` | cohort retention matrix, heatmap, revenue retention |
| `rfm_segments.csv` | customer-level RFM dataset (Power BI source) |
| `cohort_retention_pct.csv`, `cohort_counts.csv` | cohort tables (`cohort_retention_pct.csv` is the Power BI source) |
| `online_retail_II.xlsx` | raw dataset (Online Retail II, UCI) |
| `RFM_Dashboard.pbip` + `RFM_Dashboard.Report/`, `RFM_Dashboard.SemanticModel/` | Power BI Project (report + semantic model) |
| `dashboard_*.png` | dashboard page previews |
| `chart*.png` | analysis charts |

> Raw data: [Online Retail II, UCI ML Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii) — included as `online_retail_II.xlsx`; `01_Data_Cleaning.ipynb` loads it from the project folder and reproduces the cleaning.
