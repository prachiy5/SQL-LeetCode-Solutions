### Employees Project Allocation:

Table: Project

| Column Name | Type |
|-------------|------|
| project_id  | int  |
| employee_id | int  |
| workload    | int  |

- `employee_id` is the primary key (column with unique values) of this table.
- `employee_id` is a foreign key (reference column) to the `Employee` table.
- Each row of this table indicates that the employee with `employee_id` is working on the project with `project_id` and the workload of the project.

Table: Employees

| Column Name      | Type    |
|------------------|---------|
| employee_id      | int     |
| name             | varchar |
| team             | varchar |

- `employee_id` is the primary key (column with unique values) of this table.
- Each row of this table contains information about one employee.

Write a solution to find the employees who are allocated to projects with a workload that exceeds the average workload of all employees for their respective teams.

Return the result table ordered by `employee_id`, `project_id` in ascending order.

---

### Example:

#### Input:

Project table:

| project_id | employee_id | workload |
|------------|-------------|----------|
| 1          | 1           | 45       |
| 1          | 2           | 90       |
| 2          | 3           | 12       |
| 2          | 4           | 68       |

Employees table:

| employee_id | name   | team |
|-------------|--------|------|
| 1           | Khaled | A    |
| 2           | Ali    | B    |
| 3           | John   | B    |
| 4           | Doe    | A    |

#### Output:

| employee_id | project_id | employee_name | project_workload |
|-------------|------------|---------------|------------------|
| 2           | 1          | Ali           | 90               |
| 4           | 2          | Doe           | 68               |

#### Explanation:
- **Employee 1 (Khaled)**: Has a project workload of 45 and belongs to Team A (average workload = 56.50). Since 45 does not exceed 56.50, they are excluded.
- **Employee 2 (Ali)**: Has a project workload of 90 and belongs to Team B (average workload = 51.00). Since 90 exceeds 51.00, they are included.
- **Employee 3 (John)**: Has a project workload of 12 and belongs to Team B (average workload = 51.00). Since 12 does not exceed 51.00, they are excluded.
- **Employee 4 (Doe)**: Has a project workload of 68 and belongs to Team A (average workload = 56.50). Since 68 exceeds 56.50, they are included.

---

### Thought Process:
1. **Combine Data**: Use an `INNER JOIN` to combine the `Project` and `Employees` tables so we can work with all relevant columns together.
2. **Calculate Team Averages**: Use `AVG()` with a `WINDOW FUNCTION` (`OVER(PARTITION BY team)`) to calculate the average workload for each team.
3. **Filter Exceeding Workloads**: Identify rows where the workload exceeds the team's average.
4. **Format Output**: Select the required columns and rename them as specified.
5. **Sort Results**: Order the result by `employee_id` and `project_id` in ascending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        project_id,
        p.employee_id,
        workload,
        name,
        team 
    FROM 
        project p
    INNER JOIN 
        employees e
    ON 
        p.employee_id = e.employee_id
),

cte2 AS (
    SELECT 
        *,
        AVG(workload) OVER(PARTITION BY team) AS average
    FROM 
        cte
)

SELECT 
    employee_id AS EMPLOYEE_ID,
    project_id AS PROJECT_ID,
    name AS EMPLOYEE_NAME,
    workload AS PROJECT_WORKLOAD 
FROM 
    cte2
WHERE 
    workload > average
ORDER BY 
    EMPLOYEE_ID, PROJECT_ID;
