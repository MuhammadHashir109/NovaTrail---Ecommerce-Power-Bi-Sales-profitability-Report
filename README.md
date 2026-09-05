# NovaTrail Commerce — Power BI Sales & Profitability Dashboard

A six-page Power BI dashboard built for a fictional global eCommerce retailer, designed to replace manual, monthly pivot-table reporting with a single self-service source of truth for sales, profitability, fulfilment and customer performance.

![Overview page](assets/overview.png)

## 📌 Project Brief

**Case study:** NovaTrail Commerce is a global eCommerce retailer selling outdoor, lifestyle, kitchen, electronics, clothing and furniture products across 8 countries (USA, UK, Germany, France, Egypt, UAE, Saudi Arabia, India) through 4 channels (Retail, Online, Partner, Phone).

The brief: design and build a Power BI dashboard — with drill-down into supporting detail — that gives the leadership team a reliable, self-service view of sales, profitability, fulfilment, and customer behaviour, without needing to manually rebuild reports every month.

## 🗂️ Dataset

Supplied as a star schema of six related CSV files (~1% sample of order volume, names/order references de-identified):

| Table | Rows | Grain |
|---|---|---|
| `FactSales` | 100,000 | One row per order line — dates, product, customer, employee, geography, quantity, price, discount, sales amount, cost, profit, channel, payment method, order priority |
| `DimCustomer` | 1,500 | Name, signup date, geography, loyalty tier (Bronze/Silver/Gold/Platinum), gender |
| `DimDate` | 2,557 | Calendar table — year, quarter, month, weekday/weekend flags |
| `DimProduct` | 200 | Product name, category, sub-category, colour, size, standard cost, list price |
| `DimEmployee` | 50 | Name, hire date, role, home geography |
| `DimGeography` | 40 | Country, region, city |

## 📊 Dashboard Pages

### Overview
KPI snapshot — Sales, Profit, Profit %, Quantity Sold, YoY Growth, Total Customers — alongside sales by category, loyalty tier & region, and a country map.

![Overview](assets/overview.png)

### Trend Analysis
Monthly profit margin trend, quantity sold by month & loyalty tier, and sales by year & region — used to separate genuine trend from one-off blips.

![Trend Analysis](assets/trend_analysis.png)

### Sales & Profitability
Sales and profit by channel and category, plus a sales-vs-profit scatter by sub-category to spot products that generate revenue without generating margin. Includes drill-through to individual product detail.

![Sales & Profitability](assets/sales_profitability.png)

### Product Analysis
Product-level detail page (reached via drill-through) — profit margin, sales, quantity, rolling 3-month sales and discount %, plus quantity sold by country and gender split.

![Product Analysis](assets/product_analysis.png)

### Delivery Performance
Average shipping lead time by country, region, channel and order priority — the operations view, independent of revenue, used to spot fulfilment bottlenecks.

![Delivery Performance](assets/delivery_performance.png)

### Customer Loyalty Analysis
Signup-cohort table, sales by signup year, and sales by loyalty tier — used to understand retention and loyalty program effectiveness.

![Customer Loyalty Analysis](assets/customer_loyalty.png)

## 🧠 Key Design Decisions

- **Star schema**, single fact table, joined to dimensions on surrogate keys (not names) — verified zero orphaned keys before building any visuals.
- **One active date relationship** (`OrderDateKey → DimDate`); `ShipDateKey` is deliberately left inactive, with lead time pre-calculated as a column to avoid ambiguous time contexts.
- **Centralized measure table** — 29 DAX measures (Total Sales, Profit Margin, YoY Growth %, Rolling 3-Month Avg, Repeat Customers, rankings) so every page references one definition, never a re-derived one.
- **Drill-through** from category/sub-category visuals down to a dedicated Product Detail page, so the dashboard supports both a fast executive snapshot and deep investigation without cluttering the Overview with a 200-item product slicer.
- Consistent theme, navigation, and slicer panel across every page.

## 🔍 Data Quality Findings

Rather than assuming the extract was clean, the underlying tables were queried directly:

| Issue | Scale | Decision |
|---|---|---|
| Negative-profit orders | 1,556 rows (1.6%) | Kept — fully explained by heavy discounting (avg. 17.3% discount vs. 3.9% dataset average), so it's a genuine pricing signal, not an error |
| Orders predating customer signup date | 43% of order rows / ~99% of customers affected | Kept the cohort analysis on absolute Signup Year only; excluded the flawed "Years Since Signup" offset field entirely |
| Non-unique customer names | Only 143 distinct names across 1,500 customers | No impact — all customer-level measures use the `CustomerKey` surrogate key, never `CustomerName` |
| Unused local date tables | 3 auto-generated hidge tables | Left as-is; harmless, `DimDate` remains the only relationship-backed time table |

Full reasoning for each finding is in [`/report`](./report).

## 🛠️ Tech Stack

- **Power BI Desktop** — data modelling, DAX measures, report design
- **Power Query** — data shaping and load
- Source data: CSV star schema (fact + 5 dimensions)

## 📁 Repository Structure

```
├── assets/                     # Dashboard screenshots
├── data/                       # Source CSV files (fact + dimension tables)
├── report/                     # Written report — business questions, design rationale, data-quality notes
├── NovaTrail_Dashboard.pbix     # Power BI file
└── README.md
```

## 🎓 Context

This project was built as a case study assignment covering:
- Analysing business problems and formulating solutions through data visualisation
- Understanding business policies relevant to data in context
- Interrogating business data to support decision-making
- Communicating findings clearly in both a written report and a visual dashboard

## 📄 License

This project uses a synthetic/sample dataset for educational purposes. Feel free to fork and adapt for your own learning.
