# The Number of Employees Which Report to Each Employee
### Problem

#### Table: Employees

| Column Name | Type    |
|-------------|---------|
| employee_id | int     |
| name        | varchar |
| reports_to  | int     |
| age         | int     |

- `employee_id` is the primary key.
- The `reports_to` column indicates the manager an employee reports to (nullable for top-level managers).

#### Task

Find all employees who are managers. For each manager:
1. Report their `employee_id` and `name`.
2. Calculate the number of employees (`reports_count`) who report directly to them.
3. Calculate the average age (`average_age`) of their direct reports, rounded to the nearest integer.

Return the result sorted by `employee_id`.

---

### Example

#### Input

**Employees Table**:

| employee_id | name    | reports_to | age |
|-------------|---------|------------|-----|
| 9           | Hercy   | null       | 43  |
| 6           | Alice   | 9          | 41  |
| 4           | Bob     | 9          | 36  |
| 2           | Winston | null       | 37  |

#### Output

| employee_id | name  | reports_count | average_age |
|-------------|-------|---------------|-------------|
| 9           | Hercy | 2             | 39          |

#### Explanation

- `Hercy` has two direct reports: `Alice` (41 years) and `Bob` (36 years).
- Their average age is `(41 + 36) / 2 = 38.5`, rounded to `39`.
- `Winston` has no direct reports, so he is not included in the result.

---

### Thought Process

1. **Identify Managers**:
   - Employees with at least one `employee_id` listed under `reports_to` are considered managers.

2. **Group and Aggregate**:
   - Use `GROUP BY` on `reports_to` to calculate:
     - `reports_count`: Count of employees reporting to a manager.
     - `average_age`: Average age of employees reporting to a manager.

3. **Join Manager Details**:
   - Join the aggregated results with the `Employees` table to retrieve the manager's `name`.

4. **Sorting**:
   - Return the results ordered by `employee_id`.

---

### Solution

```sql
WITH cte AS (
    SELECT 
        reports_to, 
        COUNT(employee_id) AS reports_count, 
        ROUND(AVG(age), 0) AS average_age
    FROM 
        Employees
    WHERE 
        reports_to IS NOT NULL
    GROUP BY 
        reports_to
)

SELECT 
    c.reports_to AS employee_id, 
    e.name, 
    c.reports_count, 
    c.average_age
FROM 
    cte c
INNER JOIN 
    Employees e
ON 
    c.reports_to = e.employee_id
ORDER BY 
    employee_id;
```

---

### Solution Explanation

1. **CTE (Common Table Expression)**:
   - Calculate `reports_count` and `average_age` for each manager (`reports_to`).

2. **Join with Employee Details**:
   - Use `INNER JOIN` to match `reports_to` in the CTE with `employee_id` in the `Employees` table to fetch manager names.

3. **Sorting**:
   - Sort the result by `employee_id` to ensure an ordered output.

---

### Key Takeaways

- Use `GROUP BY` to aggregate data for specific columns.
- Use a CTE to simplify queries involving multiple calculations and aggregations.
- Rounding with `ROUND()` ensures numeric consistency in results.

---

### Related Topics

- SQL Aggregations
- Common Table Expressions (CTEs)
- SQL Joins
