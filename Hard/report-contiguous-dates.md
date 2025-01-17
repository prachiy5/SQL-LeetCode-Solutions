
# Report Contiguous Dates

Table: Failed
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| fail_date    | date    |
+--------------+---------+
```
fail_date is the primary key (column with unique values) for this table.
This table contains the days of failed tasks.

Table: Succeeded
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| success_date | date    |
+--------------+---------+
```
success_date is the primary key (column with unique values) for this table.
This table contains the days of succeeded tasks.

A system is running one task every day. Every task is independent of the previous tasks. The tasks can fail or succeed.

Write a solution to report the `period_state` for each continuous interval of days in the period from `2019-01-01` to `2019-12-31`.

- `period_state` is `'failed'` if tasks in this interval failed or `'succeeded'` if tasks in this interval succeeded.
- Interval of days are retrieved as `start_date` and `end_date`.

Return the result table ordered by `start_date`.

# Example

## Input

Failed table:
```
+-------------------+
| fail_date         |
+-------------------+
| 2018-12-28        |
| 2018-12-29        |
| 2019-01-04        |
| 2019-01-05        |
+-------------------+
```
Succeeded table:
```
+-------------------+
| success_date      |
+-------------------+
| 2018-12-30        |
| 2018-12-31        |
| 2019-01-01        |
| 2019-01-02        |
| 2019-01-03        |
| 2019-01-06        |
+-------------------+
```
## Output
```
+--------------+--------------+--------------+
| period_state | start_date   | end_date     |
+--------------+--------------+--------------+
| succeeded    | 2019-01-01   | 2019-01-03   |
| failed       | 2019-01-04   | 2019-01-05   |
| succeeded    | 2019-01-06   | 2019-01-06   |
+--------------+--------------+--------------+
```
## Explanation

The report ignored the system state in 2018 as we care about the system in the period `2019-01-01` to `2019-12-31`.
- From `2019-01-01` to `2019-01-03`, all tasks succeeded, and the system state was "succeeded".
- From `2019-01-04` to `2019-01-05`, all tasks failed, and the system state was "failed".
- From `2019-01-06` to `2019-01-06`, all tasks succeeded, and the system state was "succeeded".

---
# Thought Process

1. **Combine Data**:
   - Use a `UNION` query to combine `fail_date` and `success_date` into a single column `task_date` with a corresponding `period_state` column indicating success or failure.

2. **Filter for 2019**:
   - Exclude dates outside the year 2019.

3. **Group Consecutive Dates by State**:
   - Use `ROW_NUMBER()` partitioned by `period_state` to calculate a grouping key for continuous intervals.
   - Calculate a unique group identifier using the difference between `task_date` and `ROW_NUMBER()` to group consecutive rows with the same `period_state`.

4. **Aggregate Results**:
   - For each group, calculate the `start_date` (minimum date) and `end_date` (maximum date) for each interval.

5. **Return Results**:
   - Sort results by `start_date`.

---
# Query

```sql
WITH cte AS (
    SELECT fail_date AS task_date, 'failed' AS period_state
    FROM Failed
    UNION 
    SELECT success_date AS task_date, 'succeeded' AS period_state
    FROM Succeeded
),

cte2 AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY period_state ORDER BY task_date) AS rn
    FROM cte
    WHERE YEAR(task_date) = 2019
),

cte3 AS (
    SELECT period_state,
           MIN(task_date) AS start_date,
           MAX(task_date) AS end_date
    FROM cte2
    GROUP BY DATE_SUB(task_date, INTERVAL rn DAY), period_state
)

SELECT period_state, start_date, end_date
FROM cte3
ORDER BY start_date;
