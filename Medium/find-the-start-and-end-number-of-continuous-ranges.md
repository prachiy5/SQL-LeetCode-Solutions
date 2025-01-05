### Find the Start and End Number of Continuous Ranges
We are given a table:

#### Table: `Logs`

| Column Name | Type |
|-------------|------|
| log_id      | int  |

- `log_id` is the primary key and contains unique values.
- Each row represents an ID in the log table.

### Task
Write a SQL query to find the start and end numbers of continuous ranges in the `Logs` table. A continuous range is defined as a sequence of consecutive numbers in the `log_id` column.

### Requirements
- Return a result table with two columns:
  - `start_id`: The starting number of the range.
  - `end_id`: The ending number of the range.
- The result should be ordered by `start_id`.

### Example
#### Input:
**Logs table:**

| log_id |
|--------|
| 1      |
| 2      |
| 3      |
| 7      |
| 8      |
| 10     |

#### Output:

| start_id | end_id |
|----------|--------|
| 1        | 3      |
| 7        | 8      |
| 10       | 10     |

#### Explanation:
- From `1` to `3`, the numbers are consecutive, so they form a range `[1, 3]`.
- From `4` to `6`, the numbers are missing in the table.
- From `7` to `8`, the numbers are consecutive, so they form a range `[7, 8]`.
- The number `9` is missing in the table.
- The number `10` stands alone, so it forms a range `[10, 10]`.

---

### Thought Process
1. **Identify Groups of Consecutive Numbers:**
   - Use the `ROW_NUMBER()` window function to assign a sequential rank to each `log_id` in ascending order.
   - Calculate the difference between `log_id` and its rank (`log_id - rn`).
   - This difference will be constant for all consecutive numbers, effectively grouping them.
2. **Group and Aggregate:**
   - Use `GROUP BY` on the calculated difference (`log_id - rn`) to group consecutive numbers together.
   - Use `MIN(log_id)` to find the starting number (`start_id`) of each range.
   - Use `MAX(log_id)` to find the ending number (`end_id`) of each range.
3. **Order the Result:**
   - Order the ranges by `start_id`.

---

### SQL Query
```sql
WITH cte AS (
    SELECT *,
           ROW_NUMBER() OVER (ORDER BY log_id) AS rn
    FROM Logs
)

SELECT MIN(log_id) AS start_id, MAX(log_id) AS end_id
FROM cte
GROUP BY log_id - rn
ORDER BY start_id;
```

---

### Key Takeaways
1. **Window Functions:** The `ROW_NUMBER()` function helps in identifying sequential ranks and calculating group differences.
2. **Grouping Consecutive Numbers:** By grouping on `log_id - rn`, consecutive numbers are grouped together naturally.
3. **Aggregation:** Use `MIN` and `MAX` to find the start and end of each range.
4. **Ordering:** Ensure the result is ordered by `start_id` for clarity.

### Related Topics
- Window Functions in SQL
- Aggregation with `GROUP BY`
- Query Optimization for Range Detection
