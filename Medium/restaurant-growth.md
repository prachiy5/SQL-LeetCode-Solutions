### Restaurant Growth
We are given a table:

#### Table: `Customer`

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |

- `(customer_id, visited_on)` is the primary key.
- Each row contains information about a customer's transaction on a specific date.
- `amount` is the total paid by the customer on the given date.

### Task
Write a SQL query to calculate the moving average of the total amount customers paid over a 7-day window (including the current day and the six days before it). The moving average should be rounded to two decimal places.

The result should:
1. Include columns: `visited_on`, `amount` (sum of amounts for the 7-day window), and `average_amount` (rounded to 2 decimal places).
2. Be ordered by `visited_on` in ascending order.
3. Only include rows where the 7-day window is fully covered (starting from the 7th day).

### Example
#### Input:
**Customer table:**

| customer_id | name     | visited_on | amount |
|-------------|----------|------------|--------|
| 1           | Jhon     | 2019-01-01 | 100    |
| 2           | Daniel   | 2019-01-02 | 110    |
| 3           | Jade     | 2019-01-03 | 120    |
| 4           | Khaled   | 2019-01-04 | 130    |
| 5           | Winston  | 2019-01-05 | 110    |
| 6           | Elvis    | 2019-01-06 | 140    |
| 7           | Anna     | 2019-01-07 | 150    |
| 8           | Maria    | 2019-01-08 | 80     |
| 9           | Jaze     | 2019-01-09 | 110    |
| 1           | Jhon     | 2019-01-10 | 130    |
| 3           | Jade     | 2019-01-10 | 150    |

#### Output:

| visited_on | amount | average_amount |
|------------|--------|----------------|
| 2019-01-07 | 860    | 122.86         |
| 2019-01-08 | 840    | 120.00         |
| 2019-01-09 | 840    | 120.00         |
| 2019-01-10 | 1000   | 142.86         |

#### Explanation:
- **Moving Average for 2019-01-07:**
  - Amounts from `2019-01-01` to `2019-01-07`: `(100 + 110 + 120 + 130 + 110 + 140 + 150) = 860`
  - Average: `860 / 7 = 122.86`
- **Moving Average for 2019-01-08:**
  - Amounts from `2019-01-02` to `2019-01-08`: `(110 + 120 + 130 + 110 + 140 + 150 + 80) = 840`
  - Average: `840 / 7 = 120.00`
- Similarly for other dates.

---

### Thought Process
1. **Aggregate Daily Totals:**
   - Use `SUM(amount)` grouped by `visited_on` to calculate the total daily amounts.
2. **Apply a Moving Window:**
   - Use the `SUM()` and `AVG()` window functions with a 7-day range (`ROWS BETWEEN 6 PRECEDING AND CURRENT ROW`) to calculate cumulative totals and averages.
3. **Filter Results:**
   - Ensure the output only includes rows starting from the 7th day (i.e., where the 7-day window is fully covered).
4. **Round Averages:**
   - Use `ROUND()` to format the average to two decimal places.

---

### SQL Query
```sql
WITH cte1 AS (
    SELECT visited_on, SUM(amount) AS total_amount
    FROM customer 
    GROUP BY visited_on
),

cte2 AS (
    SELECT 
        visited_on,
        SUM(total_amount) OVER (
            ORDER BY visited_on 
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS amount,
        ROUND(
            AVG(total_amount) OVER (
                ORDER BY visited_on 
                ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
            ), 2
        ) AS average_amount
    FROM cte1
)

SELECT visited_on, amount, average_amount
FROM cte2 
WHERE visited_on >= (SELECT MIN(visited_on) FROM cte2) + 6;
```

---

### Key Takeaways
1. **Window Functions:** Use `SUM()` and `AVG()` with specified ranges to calculate moving totals and averages.
2. **Grouping Daily Totals:** Aggregate daily amounts as the first step before applying window functions.
3. **Date Filtering:** Use a `WHERE` clause to ensure only rows with a fully covered 7-day window are included in the output.
4. **Rounding Results:** Use `ROUND()` for formatting numerical outputs to the desired precision.

### Related Topics
- Window Functions in SQL
- Rolling Aggregates
- Data Filtering and Formatting
