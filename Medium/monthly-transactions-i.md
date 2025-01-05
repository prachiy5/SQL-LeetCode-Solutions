### Monthly Transactions I
We are given a table:

#### Table: `Transactions`

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| country       | varchar |
| state         | enum    |
| amount        | int     |
| trans_date    | date    |

- `id` is the primary key.
- The `state` column is an enum with values `"approved"` and `"declined"`.
- This table contains information about incoming transactions, including the transaction date, country, state (status), and amount.

### Task
Write a SQL query to calculate the following metrics for each month and country:
1. Total number of transactions (`trans_count`).
2. Total number of approved transactions (`approved_count`).
3. Total transaction amount (`trans_total_amount`).
4. Total amount of approved transactions (`approved_total_amount`).

Return the result table in any order.

### Example
#### Input:
**Transactions table:**

| id   | country | state    | amount | trans_date |
|------|---------|----------|--------|------------|
| 121  | US      | approved | 1000   | 2018-12-18 |
| 122  | US      | declined | 2000   | 2018-12-19 |
| 123  | US      | approved | 2000   | 2019-01-01 |
| 124  | DE      | approved | 2000   | 2019-01-07 |

#### Output:

| month    | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
|----------|---------|-------------|----------------|--------------------|-----------------------|
| 2018-12  | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01  | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01  | DE      | 1           | 1              | 2000               | 2000                  |

#### Explanation:
- For `2018-12` in `US`, there were 2 transactions totaling `3000`, of which 1 was approved with a total amount of `1000`.
- For `2019-01` in `US`, there was 1 transaction totaling `2000`, and it was approved.
- For `2019-01` in `DE`, there was 1 transaction totaling `2000`, and it was approved.

---

### Thought Process
1. **Extract Month**: Use the `DATE_FORMAT` function to extract the year and month (`%Y-%m`) from `trans_date`.
2. **Group Data**: Group transactions by `month` and `country`.
3. **Aggregate Metrics**:
   - Count total transactions (`COUNT(*)`).
   - Count approved transactions using a `CASE` statement.
   - Sum total transaction amounts (`SUM(amount)`).
   - Sum amounts of approved transactions using a `CASE` statement.
4. **Return Results**: Select and return the calculated metrics.

---

### SQL Query
```sql
SELECT 
    DATE_FORMAT(trans_date, '%Y-%m') AS month,
    country,
    COUNT(*) AS trans_count,
    SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY month, country;
```

---

### Key Takeaways
1. **Date Formatting**: Use `DATE_FORMAT` to extract specific parts of a date (e.g., year and month).
2. **Conditional Aggregation**: Use `CASE` within aggregation functions (`SUM` or `COUNT`) to calculate metrics based on specific conditions.
3. **Grouping**: Group data by multiple columns (e.g., `month`, `country`) to calculate metrics at a granular level.

### Related Topics
- Date Functions in SQL
- Conditional Aggregation with `CASE`
- Grouping and Aggregation
