### Year on Year Growth Rate

Table: user_transactions

| Column Name      | Type     |
|------------------|----------|
| transaction_id   | integer  |
| product_id       | integer  |
| spend            | decimal  |
| transaction_date | datetime |

- The `transaction_id` column uniquely identifies each row in this table.
- Each row of this table contains the transaction ID, product ID, the spend amount, and the transaction date.

Write a solution to calculate the year-on-year (YoY) growth rate for the total spend for each product.

The result table should include the following columns:
- `year`: The year of the transaction.
- `product_id`: The ID of the product.
- `curr_year_spend`: The total spend for the current year.
- `prev_year_spend`: The total spend for the previous year.
- `yoy_rate`: The year-on-year growth rate percentage, rounded to 2 decimal places.

Return the result table ordered by `product_id`, `year` in ascending order.

---

### Example:

#### Input:

`user_transactions` table:

| transaction_id | product_id | spend   | transaction_date    |
|----------------|------------|---------|---------------------|
| 1341           | 123424     | 1500.60 | 2019-12-31 12:00:00 |
| 1423           | 123424     | 1000.20 | 2020-12-31 12:00:00 |
| 1623           | 123424     | 1246.44 | 2021-12-31 12:00:00 |
| 1322           | 123424     | 2145.32 | 2022-12-31 12:00:00 |

#### Output:

| year | product_id | curr_year_spend | prev_year_spend | yoy_rate |
|------|------------|-----------------|-----------------|----------|
| 2019 | 123424     | 1500.60         | NULL            | NULL     |
| 2020 | 123424     | 1000.20         | 1500.60         | -33.35   |
| 2021 | 123424     | 1246.44         | 1000.20         | 24.62    |
| 2022 | 123424     | 2145.32         | 1246.44         | 72.12    |

#### Explanation:

1. **For 2019:**
   - `curr_year_spend` = 1500.60
   - No `prev_year_spend` recorded.
   - YoY growth rate: NULL

2. **For 2020:**
   - `curr_year_spend` = 1000.20
   - `prev_year_spend` = 1500.60
   - YoY growth rate = \(((1000.20 - 1500.60) / 1500.60) * 100\) = -33.35%

3. **For 2021:**
   - `curr_year_spend` = 1246.44
   - `prev_year_spend` = 1000.20
   - YoY growth rate = \(((1246.44 - 1000.20) / 1000.20) * 100\) = 24.62%

4. **For 2022:**
   - `curr_year_spend` = 2145.32
   - `prev_year_spend` = 1246.44
   - YoY growth rate = \(((2145.32 - 1246.44) / 1246.44) * 100\) = 72.12%

---

### Thought Process:

1. **Calculate Yearly Spend:**
   - Use `EXTRACT(YEAR FROM transaction_date)` to group transactions by year and product.
   - Sum the `spend` column to calculate the total spend for each product for each year.

2. **Get Previous Year Spend:**
   - Use the `LAG()` window function to retrieve the previous year's spend for the same product.

3. **Calculate YoY Growth Rate:**
   - Compute the YoY growth rate using the formula:
     \[ \text{YoY Rate} = \frac{\text{curr\_year\_spend} - \text{prev\_year\_spend}}{\text{prev\_year\_spend}} \times 100 \]
   - Round the result to 2 decimal places.

4. **Format the Output:**
   - Include the `year`, `product_id`, `curr_year_spend`, `prev_year_spend`, and `yoy_rate`.
   - Ensure the table is ordered by `product_id` and `year` in ascending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        EXTRACT(YEAR FROM transaction_date) AS year,
        product_id, 
        SUM(spend) AS curr_year_spend
    FROM 
        user_transactions
    GROUP BY 
        EXTRACT(YEAR FROM transaction_date), product_id
),
cte_with_lag AS (
    SELECT 
        year,
        product_id,
        curr_year_spend,
        LAG(curr_year_spend) OVER(PARTITION BY product_id ORDER BY year) AS prev_year_spend
    FROM 
        cte
)
SELECT 
    year,
    product_id,
    curr_year_spend,
    prev_year_spend,
    ROUND((curr_year_spend - prev_year_spend) * 100 / prev_year_spend, 2) AS yoy_rate
FROM 
    cte_with_lag
ORDER BY 
    product_id, year;
