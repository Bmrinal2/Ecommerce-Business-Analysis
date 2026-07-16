# Sales Profitability Analysis — Query Results

Dataset: `sales_data.xlsx` (Orders sheet, 51,290 rows)
Table referenced in all queries below: `sales`

---

## Q1. Category-Level Profit Margin vs. Overall Business Margin

**File:** `category_margin.sql`
**Question:** Which product categories are performing below and above the overall business profit margin?

| category        | overall_profit_margin_pct | category_profit_margin_pct | profitability_status |
|:----------------|---------------------------:|-----------------------------:|:----------------------|
| Furniture       | 11.61                       | 6.94                          | Below Average         |
| Office Supplies | 11.61                       | 13.69                         | Above Average          |
| Technology      | 11.61                       | 13.99                         | Above Average          |

**Takeaway:** The overall business profit margin is **11.61%**. **Furniture** sits well below this benchmark at **6.94%**, while **Office Supplies** and **Technology** both outperform the business average.

---

## Q2. Sub-Category Margin Leakage (Benchmarked Against Furniture)

**File:** `subcategory_margin.sql`
**Question:** Which sub-categories are causing margin leakage within low-performing categories?

| sub_category | net_profit_margin | profit_margin | profitability    |
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

**Takeaway:** Benchmarked against the Furniture category's own margin (7%), **Tables** is the single sub-category dragging the category down, posting a **-8%** margin — i.e., Tables is sold at a net loss. Every other sub-category in the dataset clears the 7% benchmark, with **Paper (24%)** and **Labels (20%)** the strongest performers.

---

## Q3. Shipping Mode: Cost vs. Profit Margin

**File:** `Shipping_Mode_Analysis.sql`
**Question:** How does shipping cost affect profit margins across different shipping modes?

| ship_mode      | avg_days | shipping_cost_pct | profit_margin | shipping_cost_rank | profit_margin_rank |
|:---------------|-----------:|--------------------:|----------------:|----------------------:|----------------------:|
| Same Day       | 0          | 17.41                | 11.42            | 1                      | 3                      |
| First Class    | 2          | 16.85                | 11.37            | 2                      | 1                      |
| Second Class   | 3          | 12.28                | 11.40            | 3                      | 2                      |
| Standard Class | 4          | 8.16                  | 11.75            | 4                      | 4                      |

*(`shipping_cost_rank`: 1 = highest shipping cost %; `profit_margin_rank`: 1 = lowest profit margin %)*

**Takeaway:** Faster shipping modes cost more as a share of sales — **Same Day** shipping has the highest shipping-cost ratio (17.41%) — but this does **not** translate into materially lower profit margins. **Standard Class**, the cheapest and slowest option, actually has the *highest* profit margin (11.75%), while **First Class** has the lowest (11.37%) despite Same Day being more expensive to ship. The relationship between shipping cost and profit margin is weak/non-linear across modes.

---

## Q4. Delivery Time vs. Order Priority Profitability

**File:** `Delivery_Priority_Analysis.sql`
**Question:** Does longer delivery time negatively impact profitability of priority orders?

| order_priority | avg_shipment_days | profit_margin_pct | shipment_days_rank | profit_margin_rank |
|:---------------|---------------------:|---------------------:|-----------------------:|-----------------------:|
| Low            | 6                     | 10.33                 | 1                       | 1                       |
| Medium         | 5                     | 11.87                 | 2                       | 3                       |
| High           | 3                     | 11.04                 | 3                       | 2                       |
| Critical       | 2                     | 12.60                 | 4                       | 4                       |

*(`shipment_days_rank`: 1 = longest average shipment time; `profit_margin_rank`: 1 = lowest profit margin %)*

**Takeaway:** There is a clear inverse relationship between shipment time and profit margin: **Critical**-priority orders ship fastest (2 days avg) and post the *highest* profit margin (12.60%), while **Low**-priority orders ship slowest (6 days avg) and post the *lowest* profit margin (10.33%). Longer delivery times are associated with lower profitability, though priority level itself (not delivery time alone) may also be driving the margin difference.

---

## Summary of Key Findings

1. **Furniture** is the only underperforming category (6.94% margin vs. 11.61% overall), driven almost entirely by **Tables**, which operates at a **-8%** loss margin.
2. Aside from Tables, every sub-category exceeds Furniture's internal 7% benchmark — **Paper** and **Labels** are the strongest.
3. Shipping cost as a % of sales varies widely by mode (8.16%–17.41%) but shows **no strong negative correlation** with profit margin — Standard Class is both cheapest to ship and most profitable.
4. **Critical**-priority orders are shipped fastest and are the most profitable; **Low**-priority orders are shipped slowest and are the least profitable — suggesting delivery speed (or the priority tier driving it) is associated with margin outcomes.
