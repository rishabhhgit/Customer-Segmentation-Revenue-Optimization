# RFM Customer Segmentation Analysis

**Author: Rishabh Jain**

Segmentation of ~5,900 customers from a UK-based online retailer (Dec 2009 – Dec 2011,
~780,000 transactions) using Recency, Frequency, and Monetary (RFM) analysis and monthly
cohort retention analysis to identify high-value customers, retention risks, and revenue
concentration. Built using Python, SQL, and Power BI.

## Tools Used
- **Python** (Pandas, NumPy, Matplotlib) — data cleaning, RFM scoring, segmentation logic
- **SQL** (SQLite) — segment-level aggregation and validation queries
- **Power BI** — interactive segmentation dashboard

## Method
1. Calculated Recency (days since last order), Frequency (distinct orders), and Monetary
   (total spend) per customer
2. Scored each metric 1–5 using quintiles (5 = best)
3. Mapped R/F score combinations to 9 customer segments (Champions, Loyal, At Risk, Lost, etc.)
   using a standard RFM segmentation framework
4. Built monthly acquisition cohorts (first-purchase month) and measured how much of each
   cohort returns to buy in the following 1–12 months

## Key Insights
- **Revenue is highly concentrated**: Champions and Loyal Customers make up just 46% of
  the customer base but generate 83.7% of total revenue (£14.5M of £17.4M) — a clear
  Pareto pattern worth protecting with retention efforts.
- **Champions alone (25% of customers) drive 69% of revenue** (£12M), averaging 15.7
  orders per customer and purchasing as recently as 20 days ago on average.
- **£1.09M in revenue is sitting in the "At Risk" segment** — 353 customers who were
  previously frequent buyers (avg. 7.6 orders) but haven't purchased in 344 days on
  average. One customer alone (ID 12346) represents £77.5K in historical spend and
  hasn't ordered in 326 days — a strong win-back campaign candidate.
- **"Lost" and "Hibernating" segments (25% of customers) contribute under 3% of
  revenue combined** — low priority for retention spend compared to At Risk customers.
- **Cohort analysis shows the leak is in month one**: only ~21% of a cohort buys again
  the next month, then retention plateaus at 15–22% out to month 11 — so customers who
  survive the first month largely stick. The Dec 2009 cohort (955 customers) held 35%
  at month 1 vs ~15–19% for mid-2010 cohorts, and revenue retention (~21% at M1) tracks
  customer retention closely.

## Analysis

**Revenue is concentrated in the top two segments** — Champions and Loyal Customers hold
46% of customers and 83.7% of revenue, while the bottom four segments together account
for under 7%.

![Segment revenue share](chart1_segment_revenue_share.png)

**Customer base is broad, revenue is not** — segment sizes are far more evenly spread than
the revenue they produce, which is the Pareto pattern behind the retention recommendation.

![Segment sizes](chart2_segment_sizes.png)

**Spend is driven by a small high-value tail** — most customers cluster at low monetary
values, with a thin scatter of high-spend Champions. At Risk customers sit further right
on recency, confirming they were valuable before they went quiet.

![Recency vs monetary](chart3_recency_vs_monetary.png)

**Recency and frequency move together** — the strongest cells sit in the high-recency,
high-frequency corner (Champions/Loyal), while low recency with low frequency maps to
Lost and Hibernating.

![Recency vs frequency heatmap](chart4_rf_heatmap.png)

**Cohort retention leaks in month one** — every cohort drops to ~21% by the second month,
then plateaus. The early Dec 2009 cohort holds materially better than mid-2010 cohorts.

![Cohort retention heatmap](chart5_cohort_retention.png)

## Files
- `01_Data_Cleaning.ipynb` — data cleaning (removes cancelled orders, missing customer IDs, duplicates)
- `02_rfm_analysis.ipynb` — RFM calculation, scoring, and segmentation logic
- `03_sql_analysis.ipynb` — SQL validation queries (SQLite), win-back target list, Pareto check
- `04_visualization.ipynb` — exploratory visualizations (segment revenue share, recency vs monetary, RF heatmap)
- `05_cohort_analysis.ipynb` — monthly cohort retention matrix, heatmap, and revenue retention
- `rfm_segments.csv` — full customer-level RFM dataset (Power BI data source)
- `cohort_retention_pct.csv`, `cohort_counts.csv` — cohort retention tables
  (`cohort_retention_pct.csv` is the Power BI data source)
- `online_retail_II.xlsx` — raw source dataset (Online Retail II, UCI)
- `RFM_Dashboard.pbip` — Power BI Project (semantic model + interactive report, see below)
- `dashboard_overview.png`, `dashboard_segments.png`, `dashboard_retention.png` —
  dashboard page previews

> Raw dataset: [Online Retail II, UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii)
> (included in this repo as `online_retail_II.xlsx`; `01_Data_Cleaning.ipynb` loads it from
> the project folder and reproduces the cleaning steps)

## Dashboard
Interactive Power BI dashboard monitoring customer segments, revenue contribution, the
win-back target list, and cohort retention trends. The dashboard ships as a Power BI
Project (`RFM_Dashboard.pbip`) — the open, text-based format (TMDL semantic model +
PBIR report definition) that Power BI Desktop opens natively and source-control tracks.

**Report pages**
1. **Overview** — KPI cards (total revenue, customers, avg customer value, revenue at
   risk), segment revenue share, customers by segment, and a filtered win-back table
   (At Risk + Cannot Lose Them customers ranked by spend)
2. **Segments** — customers and revenue per segment, recency vs monetary scatter by
   segment, plus cards for Champions + Loyal share of customers (46.0%), Champions +
   Loyal share of revenue (83.7%), and At Risk customer count (353)
3. **Retention Trends** — cohort retention matrix (month-since-first-purchase ×
   acquisition cohort), average retention curve, month-1 retention card (21.2%), and
   overall average-retention card (24.7%)

**Semantic model** — `RFM` and `CohortRetention` tables loaded from the CSVs via
Power Query, with 13 DAX measures (`Total Revenue`, `Revenue at Risk`, `Total Customers`,
`Champions + Loyal Revenue %`, `Champions + Loyal Customers %`, `Month 1 Retention`,
`Avg Retention`, etc.).

**To produce the `.pbix`** (Power BI Desktop on Windows — the pbix binary format is
only authorable in Desktop):
1. Copy this project to a Windows machine
2. Open `RFM_Dashboard.pbip` in Power BI Desktop (enable the *Power BI project (PBIP)
   save* option under Options if prompted)
3. When Power Query opens, set the `DataFolder` parameter to the folder containing
   `rfm_segments.csv` (default `C:\rfm-customer-segmentation`), then **Close & Apply**
4. **File → Save As → Power BI (.pbix)**

Page previews (rendered from the report definition against the same CSVs the model loads;
the `.pbip` above opens as the live, interactive report in Power BI Desktop):

**Overview**

![Overview](dashboard_overview.png)

**Segments**

![Segments](dashboard_segments.png)

**Retention Trends**

![Retention Trends](dashboard_retention.png)

## Recommendation
Prioritize a targeted win-back campaign for the 353 "At Risk" customers (£1.09M in
historical revenue) before they churn completely, protect the Champions segment with
loyalty perks given their outsized share of revenue, and add a first-month onboarding
offer — cohort analysis shows ~79% of new customers never make a second purchase.
