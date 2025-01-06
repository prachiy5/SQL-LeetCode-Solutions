
### Total Sales Amount by Year

#### Tables Description

**Table: Product**

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| product_name  | varchar |
+---------------+---------+
product_id is the primary key (column with unique values) for this table.
product_name is the name of the product.

**Table: Sales**

+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| product_id          | int     |
| period_start        | date    |
| period_end          | date    |
| average_daily_sales | int     |
+---------------------+---------+
product_id is the primary key (column with unique values) for this table. 
period_start and period_end indicate the start and end date for the sales period, and both dates are inclusive.
The average_daily_sales column holds the average daily sales amount of the items for the period.
The dates of the sales years are between 2018 to 2020.

#### Problem
Write a solution to report the total sales amount of each item for each year, with corresponding product_name, product_id, report_year, and total_amount.

Return the result table ordered by product_id and report_year.

---

#### Example

**Input:**

**Product Table:**

| product_id | product_name |
|------------|--------------|
| 1          | LC Phone     |
| 2          | LC T-Shirt   |
| 3          | LC Keychain  |

**Sales Table:**

| product_id | period_start | period_end  | average_daily_sales |
|------------|--------------|-------------|---------------------|
| 1          | 2019-01-25   | 2019-02-28  | 100                 |
| 2          | 2018-12-01   | 2020-01-01  | 10                  |
| 3          | 2019-12-01   | 2020-01-31  | 1                   |

**Output:**

| product_id | product_name | report_year | total_amount |
|------------|--------------|-------------|--------------|
| 1          | LC Phone     | 2019        | 3500         |
| 2          | LC T-Shirt   | 2018        | 310          |
| 2          | LC T-Shirt   | 2019        | 3650         |
| 2          | LC T-Shirt   | 2020        | 10           |
| 3          | LC Keychain  | 2019        | 31           |
| 3          | LC Keychain  | 2020        | 31           |

#### Explanation
1. **LC Phone:**
   - Sold between 2019-01-25 and 2019-02-28 (35 days). Total sales: `35 * 100 = 3500`.

2. **LC T-Shirt:**
   - Sold between 2018-12-01 and 2020-01-01.
   - For 2018: `31 days * 10 = 310`.
   - For 2019: `365 days * 10 = 3650`.
   - For 2020: `1 day * 10 = 10`.

3. **LC Keychain:**
   - Sold between 2019-12-01 and 2020-01-31.
   - For 2019: `31 days * 1 = 31`.
   - For 2020: `31 days * 1 = 31`.

---
### Thought Process

1. **Identify the Continuous Dates:**
   - Create a recursive CTE to generate all dates between the minimum `period_start` and maximum `period_end` in the `Sales` table.

2. **Associate Dates with Sales Data:**
   - Match each date in the generated range with the relevant product sales periods.

3. **Calculate Yearly Sales:**
   - Group the matched data by `product_id` and `year`.
   - Sum the daily sales for each year.

4. **Join Product Names:**
   - Join the `Product` table to include `product_name` in the final result.

5. **Order the Results:**
   - Ensure the output is ordered by `product_id` and `report_year`.

---
### Query

```sql
WITH RECURSIVE cte AS (
    SELECT MIN(period_start) AS period_min_date, MAX(period_end) AS period_max_date FROM sales
    UNION ALL
    SELECT DATE_ADD(period_min_date, INTERVAL 1 DAY) AS period_min_date, period_max_date FROM cte
    WHERE period_min_date < period_max_date
),

cte2 AS (
    SELECT 
        product_id,
        CAST(YEAR(period_min_date) AS CHAR) AS report_year, 
        COUNT(product_id) * average_daily_sales AS total_amount
    FROM cte c
    LEFT JOIN sales s 
    ON (c.period_min_date >= s.period_start AND c.period_min_date <= s.period_end)
    GROUP BY 1, 2
    ORDER BY 1, 2
)

SELECT 
    p.product_id,
    product_name,
    report_year,
    total_amount 
FROM cte2
INNER JOIN product p
ON cte2.product_id = p.product_id;
