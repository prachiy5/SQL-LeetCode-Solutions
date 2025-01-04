# Project Employees I

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
- `experience_years` contains the number of years of experience for each employee and is guaranteed not to be `NULL`.

#### Task
Write a query to calculate the average experience years of employees working on each project, rounded to 2 decimal places.

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

| project_id | average_years |
|------------|---------------|
| 1          | 2.00          |
| 2          | 2.50          |

**Explanation:**
- For project `1`, employees `1`, `2`, and `3` worked, with experience years `3`, `2`, and `1`. The average is `(3 + 2 + 1) / 3 = 2.00`.
- For project `2`, employees `1` and `4` worked, with experience years `3` and `2`. The average is `(3 + 2) / 2 = 2.50`.

---

### Thought Process

1. **Understand the Relationships:**
   - The `Project` table links `project_id` with `employee_id`.
   - The `Employee` table provides the `experience_years` for each `employee_id`.

2. **Join the Tables:**
   - Use an `INNER JOIN` to connect the `Project` and `Employee` tables on the `employee_id` column.

3. **Calculate Average Experience:**
   - Use the `AVG()` function to compute the average experience years for employees grouped by `project_id`.

4. **Round the Results:**
   - Use the `ROUND()` function to round the average to 2 decimal places.

5. **Group by Project ID:**
   - Use the `GROUP BY` clause to calculate the average experience for each `project_id`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT p.project_id, ROUND(AVG(e.experience_years), 2) AS average_years
FROM Project p
INNER JOIN Employee e 
ON p.employee_id = e.employee_id
GROUP BY p.project_id;
```

---

### Solution Explanation

1. **JOIN Operation:**
   - `INNER JOIN` connects the `Project` and `Employee` tables using `employee_id` to link employee experience with projects.

2. **Calculate Average:**
   - `AVG(e.experience_years)` calculates the average experience years for employees in each project.

3. **Round the Results:**
   - `ROUND(..., 2)` ensures the average is rounded to 2 decimal places.

4. **Grouping by Project:**
   - The `GROUP BY p.project_id` groups the rows by `project_id` to calculate averages for each project.

---

### Key Takeaways

- **Aggregations with GROUP BY:** Use `GROUP BY` to compute aggregate values like averages for specific groups.
- **JOINs for Data Integration:** Combine related data from multiple tables with `JOIN` operations.
- **Rounding Results:** Use the `ROUND()` function to format numerical outputs for better readability.

---

### Related Topics
- SQL JOIN Operations
- Aggregate Functions (AVG, SUM, COUNT)
- GROUP BY Clause
- Formatting Numbers with ROUND
