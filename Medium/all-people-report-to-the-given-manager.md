### All People Report to the Given Manager
We are given a table:

#### Table: `Employees`

| Column Name   | Type    |
|---------------|---------|
| employee_id   | int     |
| employee_name | varchar |
| manager_id    | int     |

- `employee_id` is the primary key and contains unique values.
- Each row indicates that the employee with ID `employee_id` reports directly to the manager with ID `manager_id`.
- The head of the company is the employee with `employee_id = 1`.

### Task
Write a SQL query to find the `employee_id` of all employees who directly or indirectly report their work to the head of the company. The indirect relation between managers does not exceed three levels of management.

The result table should include `employee_id` in any order.

### Example
#### Input:
**Employees table:**

| employee_id | employee_name | manager_id |
|-------------|---------------|------------|
| 1           | Boss          | 1          |
| 3           | Alice         | 3          |
| 2           | Bob           | 1          |
| 4           | Daniel        | 2          |
| 7           | Luis          | 4          |
| 8           | Jhon          | 3          |
| 9           | Angela        | 8          |
| 77          | Robert        | 1          |

#### Output:

| employee_id |
|-------------|
| 2           |
| 77          |
| 4           |
| 7           |

#### Explanation:
1. The head of the company is the employee with `employee_id = 1`.
2. The employees with `employee_id = 2` and `employee_id = 77` report directly to the head of the company.
3. The employee with `employee_id = 4` reports indirectly to the head of the company via `2`: `4 --> 2 --> 1`.
4. The employee with `employee_id = 7` reports indirectly to the head of the company via `4`: `7 --> 4 --> 2 --> 1`.
5. Employees with IDs `3`, `8`, and `9` do not report to the head of the company, either directly or indirectly.

---

### Thought Process
1. **Direct Reports:** Identify employees who report directly to the head of the company (`manager_id = 1`).
2. **First Level Indirect Reports:** Find employees whose managers are direct reports to the head of the company.
3. **Second Level Indirect Reports:** Find employees whose managers are in the first-level indirect reports.
4. **Exclude Head:** Ensure the result excludes the head of the company (`employee_id = 1`).
5. **Combine Results:** Use common table expressions (CTEs) to build these levels step by step.

---

### SQL Query
```sql
WITH cte AS (
    SELECT * 
    FROM employees
    WHERE manager_id IN (
        SELECT employee_id 
        FROM employees 
        WHERE manager_id = 1
    )
),

cte2 AS (
    SELECT employee_id 
    FROM employees
    WHERE manager_id IN (
        SELECT employee_id 
        FROM cte
    )
)

SELECT employee_id 
FROM cte2
WHERE employee_id != 1;
```

---

### Key Takeaways
1. **Recursive Reporting:** Build multi-level reporting hierarchies using CTEs.
2. **Indirect Relationships:** Use nested `SELECT` statements to find employees who report indirectly to the head.
3. **Exclude Head:** Ensure the query excludes the head of the company from the result.
4. **Step-by-Step Construction:** Use CTEs to break down the problem into manageable parts.

### Related Topics
- Recursive Queries in SQL
- Filtering with Nested Queries
- Common Table Expressions (CTEs)
