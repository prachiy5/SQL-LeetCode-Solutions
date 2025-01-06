# Customers With Strictly Increasing Purchases

## Table: Orders
| Column Name  | Type |
|--------------|------|
| order_id     | int  |
| customer_id  | int  |
| order_date   | date |
| price        | int  |

- `order_id` is the column with unique values for this table.
- Each row contains the ID of an order, the ID of the customer who ordered it, the date of the order, and its price.

## Task
Write a solution to report the IDs of the customers with total purchases **strictly increasing yearly**.

- The total purchases of a customer in one year is the sum of the prices of their orders in that year.
- If for some year the customer did not make any order, we consider the total purchases as 0.
- The first year to consider for each customer is the year of their first order.
- The last year to consider for each customer is the year of their last order.

Return the result table in any order.

---

# Example

### Input

#### Orders Table:
| order_id | customer_id | order_date | price |
|----------|-------------|------------|-------|
| 1        | 1           | 2019-07-01 | 1100  |
| 2        | 1           | 2019-11-01 | 1200  |
| 3        | 1           | 2020-05-26 | 3000  |
| 4        | 1           | 2021-08-31 | 3100  |
| 5        | 1           | 2022-12-07 | 4700  |
| 6        | 2           | 2015-01-01 | 700   |
| 7        | 2           | 2017-11-07 | 1000  |
| 8        | 3           | 2017-01-01 | 900   |
| 9        | 3           | 2018-11-07 | 900   |

### Output

| customer_id |
|-------------|
| 1           |

### Explanation
1. **Customer 1**:
   - The first year is 2019, and the last year is 2022.
   - Yearly purchases:
     - 2019: 1100 + 1200 = 2300
     - 2020: 3000
     - 2021: 3100
     - 2022: 4700
   - The total purchases are strictly increasing yearly, so customer 1 is included in the answer.

2. **Customer 2**:
   - The first year is 2015, and the last year is 2017.
   - Yearly purchases:
     - 2015: 700
     - 2016: 0
     - 2017: 1000
   - The total purchases are not strictly increasing (700 -> 0 -> 1000), so customer 2 is not included.

3. **Customer 3**:
   - The first year is 2017, and the last year is 2018.
   - Yearly purchases:
     - 2017: 900
     - 2018: 900
   - The total purchases are not strictly increasing (900 -> 900), so customer 3 is not included.

---

# Thought Process

1. **Calculate Yearly Total Purchases**:
   - Group the orders by `customer_id` and `year(order_date)`.
   - Calculate the total purchases for each customer per year.

2. **Identify the Year Range for Each Customer**:
   - Determine the first and last year of orders for each customer.
   - Generate all years in the range for each customer, including years with no orders.

3. **Fill Missing Years**:
   - Use a recursive query to fill in missing years between the first and last years.
   - Assign a total purchase of 0 for missing years.

4. **Check for Strictly Increasing Purchases**:
   - Use a window function (`LAG`) to compare the current year's total purchases with the previous year's.
   - Mark each year as increasing or not.

5. **Select Valid Customers**:
   - Check if all years for a customer are marked as increasing.
   - Include customers with strictly increasing yearly purchases in the result.

---

# Solution

```sql
WITH RECURSIVE cte AS (
    SELECT 
        customer_id,
        YEAR(order_date) AS year,
        SUM(price) AS total_price
    FROM orders
    GROUP BY customer_id, YEAR(order_date)
),

first_last_year AS (
    SELECT 
        customer_id, 
        MIN(year) AS first_year, 
        MAX(year) AS last_year
    FROM cte
    GROUP BY customer_id
),

year_range AS (
    SELECT 
        customer_id, 
        first_year AS year, 
        last_year
    FROM first_last_year
    UNION ALL
    SELECT 
        customer_id, 
        year + 1 AS year, 
        last_year
    FROM year_range
    WHERE year < last_year
),

all_year_price AS (
    SELECT 
        yr.customer_id,
        yr.year,
        IFNULL(c.total_price, 0) AS total_price
    FROM year_range yr
    LEFT JOIN cte c
    ON yr.customer_id = c.customer_id AND yr.year = c.year
    ORDER BY customer_id
),

increasing_price_check AS (
    SELECT 
        customer_id,
        year,
        COALESCE(total_price - LAG(total_price) OVER (PARTITION BY customer_id ORDER BY year ASC) > 0, 1) AS increasing
    FROM all_year_price
)

SELECT customer_id 
FROM increasing_price_check
GROUP BY customer_id
HAVING MIN(increasing) = 1;
```

---

# Solution Explanation

1. **First CTE (`cte`)**:
   - Group orders by `customer_id` and year.
   - Calculate the total price for each year.

2. **Second CTE (`first_last_year`)**:
   - Identify the first and last year of orders for each customer.

3. **Recursive CTE (`year_range`)**:
   - Generate all years between the first and last years for each customer.

4. **Fill Missing Years (`all_year_price`)**:
   - Left join the generated year range with yearly totals.
   - Assign a total purchase of 0 for missing years.

5. **Check for Increasing Purchases (`increasing_price_check`)**:
   - Use `LAG` to compare the current year with the previous year.
   - Mark the year as increasing if the difference is positive.

6. **Filter Valid Customers**:
   - Group by `customer_id`.
   - Include customers where all years are marked as increasing.

---

# Key Takeaways
- Use recursive queries to handle ranges dynamically.
- Window functions like `LAG` are powerful for sequential comparisons.
- Handle missing data explicitly to ensure correctness.

---

# Related Topics
- Common Table Expressions (CTEs)
- Recursive Queries
- Window Functions (`LAG`)
- Grouping and Aggregation
