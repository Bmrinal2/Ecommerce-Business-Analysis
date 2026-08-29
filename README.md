# Business Profitability Analysis

A SQL-based analysis of a global retail sales dataset, investigating where profit margins are strong, where they're leaking, and how shipping cost and delivery speed relate to profitability.

## 📊 Overview

This project answers four business questions about sales profitability using a 51,290-row order-level dataset:

1. Which product categories are performing below or above the overall business profit margin?
2. Which sub-categories are causing margin leakage within low-performing categories?
3. How does shipping cost affect profit margins across different shipping modes?
4. Does longer delivery time negatively impact profitability of priority orders?

Each question is answered with a standalone SQL query and summarized with a takeaway below.

## 🗂️ Repository Structure

```
.
├── sales_data.xlsx                    # Raw dataset (Orders, Returns, People sheets)
├── category_margin.sql                # Q1: Category vs. overall profit margin
├── subcategory_margin.sql             # Q2: Sub-category margin leakage
├── Shipping_Mode_Analysis.sql         # Q3: Shipping cost vs. profit margin by mode
├── Delivery_Priority_Analysis.sql     # Q4: Delivery time vs. order priority profitability
└── README.md                          # This file
```

## 🧱 Dataset

**Source file:** `sales_data.xlsx`
**Sheet used for analysis:** `Orders` (51,290 rows, 16 columns)

| Column          | Description                              |
|-----------------|-------------------------------------------|
| Order ID        | Unique order identifier                    |
| Order Date      | Date the order was placed                  |
| Ship Date       | Date the order shipped                     |
| Shipment days   | Days between order and ship date           |
| Ship Mode       | Shipping method (Standard, First Class, etc.) |
| Segment         | Customer segment                           |
| City / State    | Order location                             |
| Product ID      | Unique product identifier                  |
| Category        | Product category                           |
| Sub-Category    | Product sub-category                       |
| Sales           | Sales revenue                              |
| Quantity        | Units sold                                 |
| Profit          | Profit earned                              |
| Shipping Cost   | Cost to ship the order                     |
| Order Priority  | Order priority tier (Low/Medium/High/Critical) |

The `Returns` and `People` sheets are included in the workbook but are not used in the current queries.

All SQL queries in this repo reference a single flattened `sales` table built from the `Orders` sheet.

## 🔍 Analysis & Key Findings

### Q1. Category-Level Profit Margin — `category_margin.sql`

Compares each product category's margin against the overall business margin (11.61%).

| category        | overall_profit_margin_pct | category_profit_margin_pct | profitability_status |
|:----------------|---------------------------:|-----------------------------:|:----------------------|
| Furniture       | 11.61                      | 6.94                         | Below Average          |
| Office Supplies | 11.61                      | 13.69                        | Above Average          |
| Technology      | 11.61                      | 13.99                        | Above Average          |

**Finding:** Furniture underperforms the overall business margin by nearly 5 points, while Office Supplies and Technology both outperform it.

### Q2. Sub-Category Margin Leakage — `subcategory_margin.sql`

Drills into sub-categories, benchmarked against Furniture's own margin (7%), to find which are dragging the category down.

| sub_category | net_profit_margin | profit_margin | profitability   |
|:-------------|--------------------:|----------------:|:------------------|
| Tables       | 7                    | -8              | Less Profitable    |
| Machines     | 7                    | 8               | Profitable         |
| Chairs       | 7                    | 9               | Profitable         |
| Supplies     | 7                    | 9               | Profitable         |
| Storage      | 7                    | 10              | Profitable         |
| Bookcases    | 7                    | 11              | Profitable         |
| Furnishings  | 7                    | 12              | Profitable         |
| Phones       | 7                    | 13              | Profitable         |
| Appliances   | 7                    | 14              | Profitable         |
| Fasteners    | 7                    | 15              | Profitable         |
| Art          | 7                    | 16              | Profitable         |
| Binders      | 7                    | 16              | Profitable         |
| Accessories  | 7                    | 17              | Profitable         |
| Copiers      | 7                    | 17              | Profitable         |
| Envelopes    | 7                    | 17              | Profitable         |
| Labels       | 7                    | 20              | Profitable         |
| Paper        | 7                    | 24              | Profitable         |

**Finding:** **Tables** is the single sub-category sold at a net loss (-8% margin) and is the primary driver of Furniture's underperformance. Every other sub-category in the dataset is profitable, with **Paper (24%)** and **Labels (20%)** the strongest.

### Q3. Shipping Mode: Cost vs. Profit Margin — `Shipping_Mode_Analysis.sql`

Examines whether higher shipping costs (as a % of sales) correspond to lower profit margins across shipping modes.

| ship_mode      | avg_days | shipping_cost_pct | profit_margin | shipping_cost_rank | profit_margin_rank |
|:---------------|-----------:|--------------------:|----------------:|----------------------:|----------------------:|
| Same Day       | 0          | 17.41                | 11.42            | 1                      | 3                      |
| First Class    | 2          | 16.85                | 11.37            | 2                      | 1                      |
| Second Class   | 3          | 12.28                | 11.40            | 3                      | 2                      |
| Standard Class | 4          | 8.16                  | 11.75            | 4                      | 4                      |

*`shipping_cost_rank`: 1 = highest shipping cost %. `profit_margin_rank`: 1 = lowest profit margin %.*

**Finding:** Faster shipping modes cost significantly more as a share of sales, but this doesn't translate into meaningfully lower margins. **Standard Class** — the cheapest and slowest option — has the *highest* profit margin (11.75%), while **First Class** has the lowest (11.37%) despite Same Day being the priciest to ship. Shipping cost and profit margin are only weakly related here.

### Q4. Delivery Time vs. Order Priority Profitability — `Delivery_Priority_Analysis.sql`

Tests whether longer average shipment times for lower-priority orders correspond to lower profit margins.

| order_priority | avg_shipment_days | profit_margin_pct | shipment_days_rank | profit_margin_rank |
|:---------------|---------------------:|---------------------:|-----------------------:|-----------------------:|
| Low            | 6                     | 10.33                 | 1                       | 1                       |
| Medium         | 5                     | 11.87                 | 2                       | 3                       |
| High           | 3                     | 11.04                 | 3                       | 2                       |
| Critical       | 2                     | 12.60                 | 4                       | 4                       |

*`shipment_days_rank`: 1 = longest average shipment time. `profit_margin_rank`: 1 = lowest profit margin %.*

**Finding:** There's a clear inverse relationship between shipment time and profitability. **Critical**-priority orders ship fastest (2 days avg) and are the most profitable (12.60%), while **Low**-priority orders ship slowest (6 days avg) and are the least profitable (10.33%). Longer delivery times are associated with lower margins — though the priority tier itself, not delivery time alone, may be the underlying driver.

## 💡 Summary

- **Furniture** is the only underperforming category (6.94% vs. 11.61% overall), driven almost entirely by **Tables**, which runs at a **-8%** loss.
- Outside of Tables, every sub-category clears its category benchmark — **Paper** and **Labels** are standout performers.
- Shipping cost as a % of sales ranges from 8.16% to 17.41% across modes but shows **no strong negative correlation** with profit margin.
- **Order priority and delivery speed track together with profitability**: faster-shipped, higher-priority orders are consistently more profitable.

## 🛠️ Tech Stack

- **SQL** (standard ANSI syntax — window functions, CTEs, CASE expressions, subqueries)
- **Excel** (`.xlsx`) as the source data format

## ▶️ How to Reproduce

1. Load the `Orders` sheet from `sales_data.xlsx` into a SQL database (or a local SQLite/DuckDB instance) as a table named `sales`.
2. Run each `.sql` file against that table.
3. Compare output against the result tables above.


---

*Queries executed against `sales_data.xlsx` via SQLite, using the exact SQL logic in each file.*
