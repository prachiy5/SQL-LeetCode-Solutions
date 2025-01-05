### Order Two Columns Independently

Given a table `Data` with the following structure:

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| first_col   | int  |
| second_col  | int  |
+-------------+------+
```

The table may contain duplicate rows. Write a solution to independently:

1. Order `first_col` in ascending order.
2. Order `second_col` in descending order.

The result should maintain the same number of rows but reorder the columns independently as described above. The output format should match the following example:

#### Example 1:

**Input:**

`Data` table:

```
+-----------+------------+
| first_col | second_col |
+-----------+------------+
| 4         | 2          |
| 2         | 3          |
| 3         | 1          |
| 1         | 4          |
+-----------+------------+
```

**Output:**

```
+-----------+------------+
| first_col | second_col |
+-----------+------------+
| 1         | 4          |
| 2         | 3          |
| 3         | 2          |
| 4         | 1          |
+-----------+------------+
```

### Thought Process

1. The task requires us to reorder two columns independently:
   - `first_col` should be ordered in ascending order.
   - `second_col` should be ordered in descending order.
2. Use `ROW_NUMBER()` to assign a unique rank to each row for both columns, allowing us to independently order them and then recombine them using their ranks.
3. Create two Common Table Expressions (CTEs):
   - The first CTE orders `first_col` in ascending order and assigns row numbers.
   - The second CTE orders `second_col` in descending order and assigns row numbers.
4. Perform a `NATURAL JOIN` on the row numbers from the two CTEs to combine the independently ordered columns into the final result.

### Query

```sql
WITH cte AS (
    SELECT first_col, ROW_NUMBER() OVER (ORDER BY first_col) AS row_
    FROM Data
),
cte2 AS (
    SELECT second_col, ROW_NUMBER() OVER (ORDER BY second_col DESC) AS row_
    FROM Data
)

SELECT first_col, second_col
FROM cte
NATURAL JOIN cte2;
