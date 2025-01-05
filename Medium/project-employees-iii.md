### Project Employees III
We are given two tables:

#### Table: `Project`

| Column Name  | Type |
|--------------|------|
| project_id   | int  |
| employee_id  | int  |

- `(project_id, employee_id)` is the primary key.
- `employee_id` is a foreign key referencing the `Employee` table.
- Each row indicates that the employee with `employee_id` is working on the project with `project_id`.

#### Table: `Employee`

| Column Name      | Type    |
|------------------|---------|
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |

- `employee_id` is the primary key.
- Each row contains information about one employee.

### Task
Write a SQL query to report the most experienced employees in each project. If there is a tie (multiple employees with the same maximum years of experience), include all of them in the result. The result table should include the `project_id` and `employee_id` of the most experienced employees.

### Example
#### Input:
**Project table:**

| project_id  | employee_id |
|-------------|-------------|
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |

**Employee table:**

| employee_id | name   | experience_years |
|-------------|--------|------------------|
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 3                |
| 4           | Doe    | 2                |

#### Output:

| project_id  | employee_id |
|-------------|-------------|
| 1           | 1           |
| 1           | 3           |
| 2           | 1           |

#### Explanation:
- For `project_id = 1`, employees `1` and `3` have the maximum experience (3 years).
- For `project_id = 2`, employee `1` has the maximum experience (3 years).

---

### Thought Process
1. **Join the Tables**: First, join the `Project` table with the `Employee` table using the `employee_id` column to get the `experience_years` for each project-employee pair.
2. **Rank the Employees**: Use the `RANK()` window function to rank employees within each project based on their `experience_years` in descending order.
3. **Filter Top Employees**: Select only those employees who have the highest rank (i.e., `rank = 1`) within their respective projects.

---

### SQL Query
```sql
WITH ranked_employees AS (
    SELECT 
        e.employee_id, 
        p.project_id, 
        e.experience_years,
        RANK() OVER (PARTITION BY p.project_id ORDER BY e.experience_years DESC) AS r
    FROM Project p
    INNER JOIN Employee e
    ON p.employee_id = e.employee_id
)

SELECT 
    project_id, 
    employee_id
FROM ranked_employees
WHERE r = 1;
```

### Key Takeaways
1. Use `JOIN` to combine related information from multiple tables.
2. The `RANK()` function is helpful for identifying top entries within groups.
3. Filtering based on the rank (`r = 1`) ensures only the most experienced employees are selected for each project.

### Related Topics
- SQL Joins
- Window Functions
- Ranking Functions
