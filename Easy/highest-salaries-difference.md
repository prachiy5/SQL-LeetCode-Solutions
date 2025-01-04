### Question:

Table: Salaries

```plaintext
+-------------+---------+ 
| Column Name | Type    | 
+-------------+---------+ 
| emp_name    | varchar | 
| department  | varchar | 
| salary      | int     |
+-------------+---------+
```

- `(emp_name, department)` is the primary key (combination of unique values) for this table.
- Each row of this table contains `emp_name`, `department`, and `salary`.
- There will be at least one entry for the engineering and marketing departments.

**Write a solution to calculate the difference between the highest salaries in the marketing and engineering department. Output the absolute difference in salaries.**

### Example:

#### Input:

Salaries table:

```plaintext
+----------+-------------+--------+
| emp_name | department  | salary |
+----------+-------------+--------+
| Kathy    | Engineering | 50000  |
| Roy      | Marketing   | 30000  |
| Charles  | Engineering | 45000  |
| Jack     | Engineering | 85000  | 
| Benjamin | Marketing   | 34000  |
| Anthony  | Marketing   | 42000  |
| Edward   | Engineering | 102000 |
| Terry    | Engineering | 44000  |
| Evelyn   | Marketing   | 53000  |
| Arthur   | Engineering | 32000  |
+----------+-------------+--------+
```

#### Output:

```plaintext
+-------------------+
| salary_difference | 
+-------------------+
| 49000             | 
+-------------------+
```

### Explanation:
- The Engineering department has the highest salary of 102,000.
- The Marketing department has the highest salary of 53,000.
- The absolute difference between these two salaries is `102,000 - 53,000 = 49,000`.

---

### Thought Process:

1. **Identify the Problem**:
   - We need the highest salary from both the `Engineering` and `Marketing` departments.
   - Calculate the absolute difference between these two values.

2. **Steps to Solve**:
   - Use a `WITH` clause (common table expression) to rank salaries within each department using `DENSE_RANK`.
   - Extract the highest salary (rank = 1) for both `Engineering` and `Marketing`.
   - Calculate the absolute difference between these two values.

3. **Result**:
   - The result should return a single value: the absolute difference between the highest salaries.

---

### Query:

```sql
WITH cte AS (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS r
    FROM Salaries
)

SELECT ABS(
    (SELECT salary FROM cte WHERE r = 1 AND department = 'Engineering') -
    (SELECT salary FROM cte WHERE r = 1 AND department = 'Marketing')
) AS salary_difference;
```

---

### Explanation of the Query:

1. **Common Table Expression (CTE)**:
   - `DENSE_RANK()` ranks the salaries within each department in descending order.
   - The highest salary in each department gets rank `1`.

2. **Extract Top Salaries**:
   - Use subqueries to fetch the highest salary for `Engineering` and `Marketing` (where `r = 1`).

3. **Calculate Absolute Difference**:
   - Use the `ABS` function to ensure the result is positive.

4. **Output**:
   - Returns the `salary_difference` as a single value.

---

### Why This Works:
- The `DENSE_RANK` function ensures we correctly identify the top salaries within each department.
- Subqueries isolate the highest salary for each department.
- The `ABS` function ensures we return the positive difference as required.


