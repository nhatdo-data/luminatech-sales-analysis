# LuminaTech Lighting: Sales Performance Analysis

End-to-end analysis of **1.79 million sales transactions** for an Australian commercial lighting distributor, covering data cleaning, exploratory insights, hypothesis testing and multiple linear regression in Python.

> Group project for BUSA8000 Techniques in Business Analytics, Macquarie University (Session 2, 2025). Team of five.

![Sales and profit margin trend by light source](images/sales-margin-trend-by-light-source.png)

## Business problem

LuminaTech's management wanted to understand what drives sales and profitability across its products, customer districts and business chains, to inform sales strategy, customer retention and operational efficiency.

## Dataset

- Two years of transaction-level sales data (2023 and 2024): **903,702 + 884,538 rows, 38 columns**
- Fields include order, invoice and accounting dates; customer district; product codes and classifications; warehouse; order type; sales, cost, quantity and price adjustments; and currency (AUD, NZD, USD, EUR)
- A metadata workbook describing each field and code

> **Note:** the raw data was supplied for coursework and is not included in this repository. The notebook is published with its outputs so the full analysis can be read without running it.

## Approach

### 1. Data cleaning and preparation
- Merged the two yearly files, fixed an encoding issue in a column header, and dropped empty, duplicate and ~95%-missing columns
- Standardised inconsistent placeholder values (`na`, `unk`, `-`, blanks) to proper nulls and converted columns to correct numeric, date and categorical types
- Removed 7,107 duplicate rows and order types the metadata marked as *Do Not Use*
- Converted all sales and cost values to **AUD** using ATO annual exchange rates
- Imputed missing values by group mode or median (for example, warehouse by district and business chain) and dropped rows where imputation wasn't meaningful
- Investigated negative values and split them out: **returns/credits** and **intercompany sales** were separated so they wouldn't distort analysis of external customer sales
- Treated outliers with the IQR method within each item type, and applied log transformation to reduce skewness (sales skew 7.12 → 0.14)

**Result:** a clean analytical dataset of ~1.6 million rows.

### 2. Exploratory insights
- Sales and profit margin trends by light source (Traditional, LED, Accessories)
- Profit contribution by customer district
- Business chain performance: cost efficiency, units sold and average order value
- Customer segmentation by purchase frequency (weekly → one-time) and district
- One-sample t-test of LuminaTech's profit margin against an industry benchmark

### 3. Hypothesis testing (Welch's two-sample t-tests)
- Do top-revenue business areas sell more units per order than the lowest-revenue areas?
- Is there a difference in unit price between the two best-selling technology groups?
- Do the top 10 sales districts earn different profit margins from the remaining districts?

### 4. Multiple linear regression
Modelled log sales against cost, quantity and grouped categorical factors (business area, district, light source, ABC product class, technology group, order type, warehouse). Rare categories were grouped into "Other" before one-hot encoding, multicollinearity was checked with VIF (all < 5), and residual normality, homoscedasticity and linearity were checked.

![Profit by customer district](images/profit-by-customer-district.png)

## Key findings

- **Sydney, Melbourne and Brisbane generate over half of total profit**, so retaining these markets is the priority, while smaller districts present growth opportunities.
- **Traditional lighting dominates sales but is the most volatile**, while LED sales are growing. Accessories showed a temporary negative margin in early 2024 before recovering.
- **Sylvania-brand products sell at a significantly higher unit price** ($9.55 vs $3.90 for the next best-selling technology group), which points to a premium segment and a volume-driven segment that need different pricing strategies.
- **The top 10 sales districts earn lower average margins** (47.1% vs 60.4%, p < 0.001), suggesting volume in key markets may come at the cost of discounting.
- **The regression explains 88.6% of the variation in sales (adj. R² = 0.886).** A 1% rise in quantity is associated with only a 0.07% rise in sales, consistent with bulk discounting, and mid-seller and make-to-order product classes outperform the reference group.

![Correlation heatmap](images/sales-correlation-heatmap.png)

## Recommendations

- Review bulk-pricing and discount policies so volume growth translates into revenue and margin
- Protect the top three districts while targeting low-engagement districts with retention campaigns
- Look beyond "A-class" top sellers: mid-sellers and make-to-order lines show growth potential
- Monitor margins on high-cost, high-revenue product lines rather than relying on sales alone

## Limitations and next steps

- Cost is closely tied to sales by construction, so it dominates the model. A next iteration would predict sales **without** cost, or model profit margin directly, to better isolate the commercial drivers.
- With more than a million rows, almost any difference is statistically significant, so effect sizes matter more than p-values. Reporting effect sizes and confidence intervals would strengthen the tests.
- Outliers were replaced with group medians. Comparing results under other treatments (capping, or modelling the raw values) would test how robust the findings are.

## Tools

Python · pandas · NumPy · SciPy · statsmodels · Matplotlib · seaborn · Jupyter

## Repository structure

```
├── notebooks/
│   └── luminatech_sales_analysis.ipynb   # full analysis with outputs
├── images/                               # charts used in this README
└── README.md
```

## My contribution

**Nhat Thanh Thuy Do**, one of five team members:

- **Data cleaning and preparation:** worked with the team on the shared cleaning pipeline that turned 1.79M raw rows into a ~1.6M-row analytical dataset.
- **Hypothesis test (Section 3.3):** designed and ran the Welch's t-test comparing profit margins in the top 10 sales districts with the remaining districts. The test found that the highest-revenue districts earn significantly lower margins (47.1% vs 60.4%, p < 0.001), which points to discounting in key markets.
