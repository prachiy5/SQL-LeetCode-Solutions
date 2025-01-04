# Reformat Department Table

### Problem

#### Table Overview

**Department**

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| revenue     | int     |
| month       | varchar |

- `(id, month)` is the primary key of this table.
- The table contains revenue information for each department by month.
- The `month` column values range from `"Jan"` to `"Dec"`.

#### Task
Reformat the table so that:
1. Each row corresponds to a department (`id`).
2. Each column represents a month and its corresponding revenue.
3. The column names should follow the format `{Month}_Revenue`.

If a department does not have revenue data for a specific month, return `NULL` for that month.

---

### Example

**Input:**

`Department` table:

| id   | revenue | month |
|------|---------|-------|
| 1    | 8000    | Jan   |
| 2    | 9000    | Jan   |
| 3    | 10000   | Feb   |
| 1    | 7000    | Feb   |
| 1    | 6000    | Mar   |

**Output:**

| id   | Jan_Revenue | Feb_Revenue | Mar_Revenue | ... | Dec_Revenue |
|------|-------------|-------------|-------------|-----|-------------|
| 1    | 8000        | 7000        | 6000        | ... | NULL        |
| 2    | 9000        | NULL        | NULL        | ... | NULL        |
| 3    | NULL        | 10000       | NULL        | ... | NULL        |

**Explanation:**
- Department `1` has revenue data for `Jan`, `Feb`, and `Mar`.
- Department `2` has revenue data only for `Jan`.
- Department `3` has revenue data only for `Feb`.
- Other months for all departments have `NULL` revenue.

---

### Thought Process

1. **Use Conditional Aggregation:**
   - Use a `CASE` statement to assign revenue values based on the `month`.
   - Assign `NULL` for months without data.

2. **Aggregate by Department:**
   - Group rows by `id` to consolidate revenue data for each department.

3. **Create Month-Specific Columns:**
   - Create columns for each month (`Jan_Revenue`, `Feb_Revenue`, etc.) by summing revenue values filtered by `CASE` conditions.

4. **Sort Results:**
   - Order the result table by `id`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT id,
  SUM(CASE WHEN month = 'Jan' THEN revenue ELSE NULL END) AS Jan_Revenue,
  SUM(CASE WHEN month = 'Feb' THEN revenue ELSE NULL END) AS Feb_Revenue,
  SUM(CASE WHEN month = 'Mar' THEN revenue ELSE NULL END) AS Mar_Revenue,
  SUM(CASE WHEN month = 'Apr' THEN revenue ELSE NULL END) AS Apr_Revenue,
  SUM(CASE WHEN month = 'May' THEN revenue ELSE NULL END) AS May_Revenue,
  SUM(CASE WHEN month = 'Jun' THEN revenue ELSE NULL END) AS Jun_Revenue,
  SUM(CASE WHEN month = 'Jul' THEN revenue ELSE NULL END) AS Jul_Revenue,
  SUM(CASE WHEN month = 'Aug' THEN revenue ELSE NULL END) AS Aug_Revenue,
  SUM(CASE WHEN month = 'Sep' THEN revenue ELSE NULL END) AS Sep_Revenue,
  SUM(CASE WHEN month = 'Oct' THEN revenue ELSE NULL END) AS Oct_Revenue,
  SUM(CASE WHEN month = 'Nov' THEN revenue ELSE NULL END) AS Nov_Revenue,
  SUM(CASE WHEN month = 'Dec' THEN revenue ELSE NULL END) AS Dec_Revenue
FROM Department
GROUP BY id
ORDER BY id;
```

---

### Solution Explanation

1. **Conditional Aggregation:**
   - Use `CASE` statements to filter revenue for each month and assign it to the respective column.

2. **Summing Revenue:**
   - Use `SUM()` to aggregate the revenue for each department and month.

3. **Grouping and Sorting:**
   - Group by `id` to consolidate rows for each department.
   - Order the results by `id` to maintain a structured and sorted output.

---

### Key Takeaways

- **Pivoting Data:** Use `CASE` and `SUM` to create columns based on row values.
- **NULL Handling:** Ensure columns show `NULL` for missing data.
- **Aggregation:** Grouping is essential for consolidating data into a pivoted format.

---

### Related Topics
- SQL Conditional Aggregation
- Pivoting Data in SQL
- Handling NULL Values in SQL
