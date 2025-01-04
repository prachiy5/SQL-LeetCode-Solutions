# Project Employees II

### Problem

#### Tables Overview

**Project**

| Column Name  | Type |
|--------------|------|
| project_id   | int  |
| employee_id  | int  |

- `(project_id, employee_id)` is the primary key.
- Each row indicates that an employee (`employee_id`) is working on a specific project (`project_id`).

**Employee**

| Column Name      | Type    |
|------------------|---------|
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |

- `employee_id` is the primary key.
- Each row contains information about one employee.

#### Task
Find the project(s) that have the most employees.
- Return the `project_id` of the project(s) with the highest number of employees.
- If multiple projects tie for the maximum number of employees, include all of them.

---

### Example

**Input:**

`Project` table:

| project_id | employee_id |
|------------|-------------|
| 1          | 1           |
| 1          | 2           |
| 1          | 3           |
| 2          | 1           |
| 2          | 4           |

`Employee` table:

| employee_id | name   | experience_years |
|-------------|--------|------------------|
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |

**Output:**

| project_id |
|------------|
| 1          |

**Explanation:**
- Project `1` has 3 employees (IDs `1`, `2`, `3`).
- Project `2` has 2 employees (IDs `1`, `4`).
- Since project `1` has the most employees, it is included in the result.

---

### Thought Process

1. **Group and Count Employees by Project:**
   - Use `GROUP BY` on `project_id` to calculate the number of employees for each project.
   - Use `COUNT(employee_id)` to determine the number of employees in each project.

2. **Rank Projects by Employee Count:**
   - Use the `RANK()` window function to rank projects based on their employee counts in descending order.

3. **Filter for the Top Projects:**
   - Select only the projects with the highest rank (`r = 1`).

4. **Return Result:**
   - Output the `project_id` of the top-ranked projects.

---

### Solution

```sql
WITH cte AS (
  SELECT 
    project_id, 
    RANK() OVER (ORDER BY COUNT(employee_id) DESC) AS r
  FROM Project
  GROUP BY project_id
)

SELECT project_id
FROM cte
WHERE r = 1;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Groups the `Project` table by `project_id`.
   - Calculates the number of employees for each project using `COUNT(employee_id)`.
   - Assigns a rank (`r`) to each project based on the employee count in descending order.

2. **Filter for Top Projects:**
   - Selects projects where `r = 1`, ensuring only the projects with the maximum number of employees are included.

3. **Final Output:**
   - Returns the `project_id` of the project(s) with the most employees.

---

### Key Takeaways

- **Window Functions (`RANK()`):** Useful for ranking groups based on aggregate values.
- **GROUP BY with COUNT:** Efficient for aggregating and calculating group-specific metrics.
- **Flexible Filtering:** Combine `RANK()` with a `WHERE` clause to extract top-performing groups.

---

### Related Topics
- SQL Window Functions
- Aggregate Functions
- GROUP BY and HAVING
