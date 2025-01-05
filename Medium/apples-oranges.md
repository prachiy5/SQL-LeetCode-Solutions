### Apples Oranges
We are given a table:

#### Table: `Sales`

| Column Name   | Type    |
|---------------|---------|
| sale_date     | date    |
| fruit         | enum    |
| sold_num      | int     |

- `(sale_date, fruit)` is the primary key.
- This table records the sales of two fruits: "apples" and "oranges", on each day (`sale_date`).
- The `sold_num` column indicates the number of fruits sold on that day.

### Task
Write a SQL query to compute the daily difference between the number of apples and oranges sold. The difference is calculated as:

\[
\text{diff} = \text{apples\_sold} - \text{oranges\_sold}
\]

Return the result table ordered by `sale_date` in ascending order.

### Example
#### Input:
**Sales table:**

| sale_date  | fruit    | sold_num |
|------------|----------|----------|
| 2020-05-01 | apples   | 10       |
| 2020-05-01 | oranges  | 8        |
| 2020-05-02 | apples   | 15       |
| 2020-05-02 | oranges  | 15       |
| 2020-05-03 | apples   | 20       |
| 2020-05-03 | oranges  | 0        |
| 2020-05-04 | apples   | 15       |
| 2020-05-04 | oranges  | 16       |

#### Output:

| sale_date  | diff |
|------------|------|
| 2020-05-01 | 2    |
| 2020-05-02 | 0    |
| 2020-05-03 | 20   |
| 2020-05-04 | -1   |

#### Explanation:
- **2020-05-01:** Apples: 10, Oranges: 8. Difference: \(10 - 8 = 2\).
- **2020-05-02:** Apples: 15, Oranges: 15. Difference: \(15 - 15 = 0\).
- **2020-05-03:** Apples: 20, Oranges: 0. Difference: \(20 - 0 = 20\).
- **2020-05-04:** Apples: 15, Oranges: 16. Difference: \(15 - 16 = -1\).

---

### Thought Process
1. **Filter Data for Each Fruit:**
   - Create two separate datasets for apples and oranges by filtering rows based on the `fruit` column.

2. **Calculate Daily Sales for Each Fruit:**
   - Group by `sale_date` to calculate the total sales for apples and oranges separately.

3. **Join the Results:**
   - Join the results for apples and oranges on `sale_date`.

4. **Compute the Difference:**
   - Subtract the number of oranges sold from the number of apples sold for each day.

5. **Sort the Result:**
   - Order the result by `sale_date` in ascending order.

---

### SQL Query
```sql
WITH apples AS (
    SELECT sale_date, fruit, sold_num AS apple_sales 
    FROM sales
    WHERE fruit = 'apples'
    GROUP BY sale_date
),

oranges AS (
    SELECT sale_date, fruit, sold_num AS oranges_sales 
    FROM sales
    WHERE fruit = 'oranges'
    GROUP BY sale_date
)

SELECT 
    apples.sale_date, 
    apple_sales - oranges_sales AS diff
FROM apples, oranges
WHERE apples.sale_date = oranges.sale_date
GROUP BY apples.sale_date
ORDER BY apples.sale_date;
```

---

### Key Points
1. **Common Table Expressions (CTEs):**
   - Used to separately calculate daily sales for apples and oranges, improving query clarity.

2. **Join on `sale_date`:**
   - Combines the apple and orange sales for each day to calculate the difference.

3. **Subtraction for `diff`:**
   - The difference is calculated directly in the `SELECT` clause.

4. **Sorting:**
   - Ensures the output is ordered by `sale_date` as required.

### Related Topics
- SQL Joins
- Common Table Expressions (CTEs)
- Conditional Aggregation
