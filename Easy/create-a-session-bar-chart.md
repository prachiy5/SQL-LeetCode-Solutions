# Create a Session Bar Chart

### Problem

#### Table: Sessions

| Column Name  | Type    |
|--------------|---------|
| session_id   | int     |
| duration     | int     |

- `session_id` is the column with unique values for this table.
- `duration` is the time in seconds that a user has spent in the application.

### Task

Classify session durations into the following bins:
1. `[0-5>` minutes
2. `[5-10>` minutes
3. `[10-15>` minutes
4. `15 minutes or more`

For each bin, count the total number of sessions. Return the result table in any order.

### Example

#### Input

**Sessions** table:

| session_id  | duration  |
|-------------|-----------|
| 1           | 30        |
| 2           | 199       |
| 3           | 299       |
| 4           | 580       |
| 5           | 1000      |

#### Output

| bin          | total     |
|--------------|-----------|
| [0-5>        | 3         |
| [5-10>       | 1         |
| [10-15>      | 0         |
| 15 or more   | 1         |

#### Explanation

- **[0-5> minutes:** Session durations less than 5 minutes (session IDs: 1, 2, 3).
- **[5-10> minutes:** Session durations between 5 and 10 minutes (session ID: 4).
- **[10-15> minutes:** No session falls in this range.
- **15 minutes or more:** Session durations greater than 15 minutes (session ID: 5).

---

### Thought Process

1. **Convert Duration to Minutes:**
   - Divide `duration` by 60 to convert seconds to minutes.

2. **Group into Bins:**
   - Create bins `[0-5>`, `[5-10>`, `[10-15>`, and `15 or more`.
   - Use conditional logic (`CASE` statements) to assign sessions to bins.

3. **Count Sessions per Bin:**
   - Use `SUM` with conditions to count sessions in each bin.

4. **Combine Results:**
   - Use `UNION ALL` to combine results for each bin into one table.

---

### Solution

```sql
WITH cte AS (
    SELECT duration / 60 AS mins
    FROM Sessions
)

SELECT '[0-5>' AS bin, SUM(CASE WHEN mins < 5 THEN 1 ELSE 0 END) AS total
FROM cte
UNION ALL
SELECT '[5-10>' AS bin, SUM(CASE WHEN mins >= 5 AND mins < 10 THEN 1 ELSE 0 END) AS total
FROM cte
UNION ALL
SELECT '[10-15>' AS bin, SUM(CASE WHEN mins >= 10 AND mins < 15 THEN 1 ELSE 0 END) AS total
FROM cte
UNION ALL
SELECT '15 or more' AS bin, SUM(CASE WHEN mins >= 15 THEN 1 ELSE 0 END) AS total
FROM cte;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Converts `duration` from seconds to minutes by dividing by 60.

2. **CASE Statements:**
   - Categorize sessions into bins based on the `mins` value:
     - `[0-5>`: `mins < 5`
     - `[5-10>`: `mins >= 5 AND mins < 10`
     - `[10-15>`: `mins >= 10 AND mins < 15`
     - `15 or more`: `mins >= 15`

3. **Count Sessions:**
   - Use `SUM` to count sessions in each bin. Each `CASE` statement evaluates to `1` or `0` to include or exclude a session in the total.

4. **Combine Results:**
   - Use `UNION ALL` to merge the counts for all bins into a single result table.

---

### Key Takeaways

- **Bin Creation:** Use `CASE` statements to group data into predefined ranges.
- **Conditional Counting:** Use `SUM` with `CASE` to count rows conditionally.
- **UNION ALL:** Combine multiple queries into a single result table.

---

### Related Topics

- Conditional Aggregation
- Common Table Expressions (CTEs)
- SQL UNION
