### CEO Subordinate Hierarchy

Table: Employees

| Column Name   | Type    |
|---------------|---------|
| employee_id   | int     |
| employee_name | varchar |
| manager_id    | int     |
| salary        | int     |

- `employee_id` is the unique identifier for this table.
- `manager_id` is the `employee_id` of the employee's manager. The CEO has a `NULL` `manager_id`.

Write a solution to find subordinates of the CEO (both direct and indirect), along with their level in the hierarchy and their salary difference from the CEO.

The result should have the following columns:
- `subordinate_id`: The `employee_id` of the subordinate.
- `subordinate_name`: The name of the subordinate.
- `hierarchy_level`: The level of the subordinate in the hierarchy (1 for direct reports, 2 for their direct reports, and so on).
- `salary_difference`: The difference between the subordinate's salary and the CEO's salary.

Return the result table ordered by `hierarchy_level` ascending, and then by `subordinate_id` ascending.

---

### Example:

#### Input:

Employees table:

| employee_id | employee_name  | manager_id | salary  |
|-------------|----------------|------------|---------|
| 1           | Alice          | NULL       | 150000  |
| 2           | Bob            | 1          | 120000  |
| 3           | Charlie        | 1          | 110000  |
| 4           | David          | 2          | 105000  |
| 5           | Eve            | 2          | 100000  |
| 6           | Frank          | 3          | 95000   |
| 7           | Grace          | 3          | 98000   |
| 8           | Helen          | 5          | 90000   |

#### Output:

| subordinate_id | subordinate_name | hierarchy_level | salary_difference |
|----------------|------------------|-----------------|-------------------|
| 2              | Bob              | 1               | -30000            |
| 3              | Charlie          | 1               | -40000            |
| 4              | David            | 2               | -45000            |
| 5              | Eve              | 2               | -50000            |
| 6              | Frank            | 2               | -55000            |
| 7              | Grace            | 2               | -52000            |
| 8              | Helen            | 3               | -60000            |

#### Explanation:
- Bob and Charlie are direct subordinates of Alice (CEO) and thus have a `hierarchy_level` of 1.
- David and Eve report to Bob, while Frank and Grace report to Charlie, making them second-level subordinates (`hierarchy_level` 2).
- Helen reports to Eve, making Helen a third-level subordinate (`hierarchy_level` 3).
- Salary differences are calculated relative to Alice's salary of 150000.
- The result is ordered by `hierarchy_level` ascending, and then by `subordinate_id` ascending.

---

### Thought Process:
1. **Identify the CEO:**
   - Find the employee with a `NULL` `manager_id` (the CEO).
2. **Recursive CTE:**
   - Use a recursive common table expression (CTE) to traverse the hierarchy.
   - Start with the CEO and increment the `hierarchy_level` as we traverse down the hierarchy.
3. **Calculate Salary Difference:**
   - For each subordinate, compute the difference between their salary and the CEO's salary.
4. **Filter and Order Results:**
   - Exclude the CEO from the results (since they are not a subordinate).
   - Order the results by `hierarchy_level` ascending, and then by `subordinate_id` ascending.

---

### Query:

```sql
WITH RECURSIVE cte AS (
    SELECT 
        employee_id, 
        employee_name,
        0 AS hierarchy_level,
        salary
    FROM 
        employees 
    WHERE 
        manager_id IS NULL

    UNION ALL

    SELECT 
        e.employee_id, 
        e.employee_name, 
        c.hierarchy_level + 1 AS hierarchy_level,
        e.salary
    FROM 
        employees e
    JOIN 
        cte c
    ON 
        c.employee_id = e.manager_id
)

SELECT 
    employee_id AS subordinate_id,
    employee_name AS subordinate_name,
    hierarchy_level,
    salary - (SELECT salary FROM employees WHERE manager_id IS NULL) AS salary_difference
FROM 
    cte
WHERE 
    hierarchy_level > 0
ORDER BY 
    hierarchy_level, subordinate_id;
