# Second Highest Salary II

You are given a table `employees` with the following structure:

| Column Name | Type    |
|-------------|---------|
| emp_id      | int     |
| salary      | int     |
| dept        | varchar |

- `emp_id` is the unique key for this table.
- Each row contains information about an employee including their ID, salary, and department.

### Task
Find the employees who earn the **second-highest salary** in each department. If multiple employees have the same second-highest salary, include all employees with that salary.

Return the result table ordered by `emp_id` in ascending order.

### Input Example
#### employees Table:

| emp_id | salary | dept      |
|--------|--------|-----------|
| 1      | 70000  | Sales     |
| 2      | 80000  | Sales     |
| 3      | 80000  | Sales     |
| 4      | 90000  | Sales     |
| 5      | 55000  | IT        |
| 6      | 65000  | IT        |
| 7      | 65000  | IT        |
| 8      | 50000  | Marketing |
| 9      | 55000  | Marketing |
| 10     | 55000  | HR        |

### Output Example

| emp_id | dept      |
|--------|-----------|
| 2      | Sales     |
| 3      | Sales     |
| 5      | IT        |
| 8      | Marketing |

### Explanation

1. **Sales Department**:
   - Highest salary: `90000` (emp_id: 4).
   - Second-highest salary: `80000` (emp_id: 2, 3).
   - Both employees with salary `80000` are included.

2. **IT Department**:
   - Highest salary: `65000` (emp_id: 6, 7).
   - Second-highest salary: `55000` (emp_id: 5).
   - Only emp_id `5` is included as they have the second-highest salary.

3. **Marketing Department**:
   - Highest salary: `55000` (emp_id: 9).
   - Second-highest salary: `50000` (emp_id: 8).
   - Employee `8` is included.

4. **HR Department**:
   - Only one employee exists, so it is not included in the result as the department has fewer than 2 employees.

### Thought Process

1. **Rank Salaries within Departments**:
   - Use the `DENSE_RANK()` function to rank employees' salaries within each department in descending order.

2. **Filter for Second-Highest Salary**:
   - Select rows where the rank is `2` for the second-highest salary in each department.

3. **Order the Results**:
   - Return the `emp_id` and `dept` columns, ordered by `emp_id` in ascending order.

### Query
```sql
WITH cte AS (
    SELECT 
        emp_id, 
        salary, 
        dept,
        DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS r
    FROM 
        employees
)

SELECT 
    emp_id, 
    dept
FROM 
    cte
WHERE 
    r = 2
ORDER BY 
    emp_id;
