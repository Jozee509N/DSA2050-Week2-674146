# DSA 2050 – Week 2 SQL Lab

**Name:** Joseph Ngui
**Student ID:** 674146
**Course:** DSA 2050
**Notebook:** `DSA_2050_Week2_SQL_Lab.ipynb`

---

## Objective

This lab explores how to combine data from different sources (two CSV files and one JSON file) using Python (pandas) and SQL (SQLite), and how to check that the combined data can be trusted. Working with small customer, order and region datasets for a retail business, I practised basic SQL queries, aggregation with `GROUP BY`, and joining tables. The main goal was to detect data-quality problems, namely duplicate keys and unmatched records, see how they distort a `JOIN`, fix them, and confirm the fix by reconciling row counts and sales totals before producing business KPIs by product category, customer segment and region.

---

## Data Sources

| File | Description | Rows |
|---|---|---|
| `customers.csv` | Customer ID, name, segment (Retail / Corporate / SME) and region code | 11 (10 unique customers) |
| `orders.csv` | Order ID, customer ID, order amount (KSh) and product category | 15 |
| `regions.json` | Lookup of region code to region name (NRB, MSA, KSM, NKR) | 4 |

All data was loaded into a SQLite database (`retail_lab.db`) so it could be queried with SQL.

---

## Steps Performed

1. Created the three datasets and saved them as CSV/JSON files.
2. Inspected the sources and compared their grain (one row per customer vs. one row per order).
3. Checked for data-quality problems (duplicate IDs, orders with no matching customer).
4. Loaded the data into SQLite and ran basic `SELECT`, `WHERE` and `AND` queries.
5. Calculated KPIs (order count, total sales, average order value) by product category.
6. Joined orders to customers and checked row counts and totals.
7. Removed the duplicate customer record and re-ran the join to confirm it was fixed.
8. Calculated KPIs by customer segment, then merged in the JSON region lookup to analyse sales by region.

---

## Key Findings

### 1. A duplicate customer record inflated the JOIN results
Customer `C004` (David) appeared twice in `customers.csv`. Joining orders to customers produced **16 rows instead of 15**, and total sales rose from **KSh 113,500 to KSh 122,600**, an overstatement of KSh 9,100 (the value of order `O005`, which was duplicated). After removing the duplicate with `drop_duplicates()`, the join returned 15 rows and the total matched the original KSh 113,500. This shows why row counts and totals should always be reconciled after a join.

### 2. Corporate customers generated the most sales, and one order has no customer
| Segment | Orders | Total Sales (KSh) | Avg. Order Value (KSh) |
|---|---|---|---|
| Corporate | 4 | 46,800 | 11,700 |
| Retail | 6 | 29,600 | 4,933 |
| SME | 4 | 27,200 | 6,800 |
| Unmatched | 1 | 9,900 | 9,900 |

Corporate had the highest total sales and the highest average order value. Retail had the most orders but the lowest average value. Order `O015` belongs to customer `C999`, who does not exist in the customer table, so KSh 9,900 cannot be assigned to any segment or region. It was kept visible as "Unmatched" rather than deleted, so it can be investigated.

### 3. Nairobi had the highest regional sales, but this does not prove it is the "best" region
| Region | Orders | Total Sales (KSh) |
|---|---|---|
| Nairobi | 7 | 39,800 |
| Mombasa | 3 | 23,300 |
| Nakuru | 2 | 20,400 |
| Kisumu | 2 | 20,100 |

Nairobi led on both order count and sales. However, the data only shows gross revenue: it does not include the cost of serving each region, so profitability is unknown. Nairobi may also simply have a larger customer base. In addition, the unmatched KSh 9,900 order is excluded from the regional view. By product category, Electronics was the strongest (KSh 51,500 from 6 orders) and Home the weakest (KSh 19,500 from 4 orders).

---

## Tools Used

- Python 3 (pandas, sqlite3, json)
- SQLite
- Jupyter Notebook
