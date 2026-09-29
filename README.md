# Inventory ABC Classification, EOQ & Reorder Point Dashboard

An Excel-based inventory analytics project that classifies 3,801 products by sales value, calculates optimal order quantities, and flags the products that need the closest attention — built entirely with native Excel tools (no Power BI, no add-ins).

## The problem

A retailer with thousands of SKUs can't manage every product with the same level of attention. Spending equal effort monitoring a low-volume item and a top seller wastes time and ties up capital in the wrong places. This project answers three questions:

1. **Which products actually matter?** (ABC classification)
2. **How much should be ordered each time, for the products that matter?** (Economic Order Quantity)
3. **When should a new order be triggered, and which of those products are hardest to predict?** (Reorder Point + demand variability ranking)

## Data source

[Online Retail Dataset](https://www.kaggle.com/datasets/vijayuv/onlineretail) (Kaggle) — real transaction-level sales data from a UK-based online retailer. 541,909 raw transaction lines, cleaned down to 3,801 unique products after removing cancelled orders, non-product entries (postage, fees), and rows with missing descriptions or non-positive prices. The full raw dataset is available at the Kaggle link above; the cleaning and classification logic is in the `Clasificación ABC` and `Registros descartados` sheets of the workbook.

## Methodology

**ABC Classification** — Products are ranked by annual sales value (descending) and grouped by cumulative share of total value: Category A = up to 80% of cumulative value, B = 80–95%, C = 95–100%. This is the standard Pareto-based approach used in inventory management; the 80% threshold is an industry convention, not a value derived from the data.

**Economic Order Quantity (EOQ)** — For each product: `EOQ = √(2 × Annual Demand × Order Cost / Holding Cost)`. Order cost (£15 per order) and holding cost (20% of unit price per product) are documented assumptions, not present in the raw data — stated explicitly in the workbook rather than hidden inside a formula.

**Reorder Point (ROP)** — `ROP = (Daily Demand × Lead Time) + Safety Stock`, where Safety Stock accounts for demand variability using a 95% service level (`1.65 × Standard Deviation × √Lead Time`). Lead time is a documented assumption (7 days).

**Demand variability ranking (CV)** — For the Top 20 risk table, Category A products are ranked by Coefficient of Variation (`CV = Standard Deviation / Daily Demand`) — a relative measure of how unpredictable a product's demand is, independent of its sales volume.

## Key findings

- **21.44% of products (Category A, 815 SKUs) generate 79.99% of total sales value** — a near-textbook Pareto split.
- **53.17% of products (Category C, 2,021 SKUs) generate only 5.00% of value** — more than half the catalog barely moves the needle, and doesn't justify tight inventory control.
- **Median over mean for the Reorder Point KPI:** one product (StockCode 23166) had a single transaction of 74,215 units — nearly 260× its typical order line (median: 8 units). Because the standard deviation calculation is sensitive to this kind of outlier, it inflated the average Reorder Point for Category A to 235.6 units. The median (142 units) is not affected by that single outlier and better represents a typical Category A product. The outlier was kept in the dataset (no evidence it was a data entry error) but documented as the reason the median is reported instead of the mean.

## Dashboard walkthrough

| Element | What it shows |
|---|---|
| KPI cards | Total SKUs analyzed, % of SKUs in Category A, % of value in Category A, median Reorder Point (Category A) |
| Pareto chart | Cumulative sales value curve with an 80% reference line — visually confirms where Category A ends |
| ABC distribution chart | % of SKUs vs. % of value, side by side per category — makes the imbalance visible at a glance |
| Top 20 risk table | The 20 Category A products with the highest demand variability (CV) — the products most worth watching closely, even within the "important" group |
| Category filter (slicer) | Filters the full product table by category (A/B/C) and updates a small indicator panel (SKU count, total value, average EOQ, average Reorder Point) for the selected category |

## Assumptions & limitations

- Order cost (£15/order), holding cost (20% of unit price), and lead time (7 days) are documented assumptions, not values present in the raw dataset.
- The dataset has no live inventory levels — it's historical sales data, so Reorder Point and EOQ are planning estimates, not a live stock-tracking system.
- 236 products (6.2%) have zero calculated standard deviation: 130 because they have only one recorded sale (too little data to estimate variability, treated conservatively as zero safety stock), and 106 because every recorded sale was for the exact same quantity (genuinely stable demand).
- The 80% ABC threshold, the 95% service level for safety stock, and the CV-based risk ranking are standard methodological choices, not the only valid ones.

## Repository structure

```
01-inventory-abc-eoq-dashboard/
├── README.md
├── excel/
│   └── dashboard_inventarios.xlsx # full workbook: raw data, calculations, dashboard
├── report/
│   └── reporte_inventarios.pdf    # 1-2 page findings summary
└── images/
    ├── dashboard_overview.png
    ├── pareto_chart.png
    └── top20_risk_table.png
```

Raw and cleaned transaction data are not duplicated here — the raw dataset is public on Kaggle (linked above), and the cleaning/classification logic is fully contained in the workbook itself.

## Tools & skills

Excel: structured tables (`Tabla`), array/matrix formulas (`Ctrl+Shift+Enter`), `SUMAR.SI.CONJUNTO`, `SI.ERROR`, `K.ESIMO.MAYOR` + `INDICE`/`COINCIDIR`, `SUBTOTALES`, PivotTables, slicers, conditional formatting, combo charts with a secondary axis.

## Author

Daniel Ricardo Forero Guerrero — Industrial Engineering student, Universidad El Bosque (Bogotá, Colombia).
[LinkedIn] · [Portfolio]
