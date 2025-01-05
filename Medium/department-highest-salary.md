
### Department Highest Salary

#### Table: Employee

| Column Name  | Type    |
|--------------|---------|
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

- `id` is the primary key (column with unique values) for this table.
- `departmentId` is a foreign key referencing the ID from the Department table.
- Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

#### Table: Department

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key (column with unique values) for this table.
- It is guaranteed that `department name` is not NULL.
- Each row of this table indicates the ID of a department and its name.

#### Task

Write a solution to find employees who have the highest salary in each of the departments.

#### Return
- The result table in any order.

#### Example:

Input:

**Employee table:**

| id | name  | salary | departmentId |
|----|-------|--------|--------------|
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |

**Department table:**

| id | name  |
|----|-------|
| 1  | IT    |
| 2  | Sales |

Output:

| Department | Employee | Salary |
|------------|----------|--------|
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |

#### Explanation
- Max and Jim both have the highest salary in the IT department.
- Henry has the highest salary in the Sales department.

---

### Thought Process:

1. **Join Tables**: Combine the `Employee` and `Department` tables to include department names alongside employee details.
2. **Identify Highest Salaries**: Use a window function to rank employees within each department based on their salary in descending order.
3. **Filter Top Salaries**: Select only those employees who have the highest salary in their respective departments.
4. **Return Results**: Format the result to show the department name, employee name, and salary, sorted as required.

---

### Query:

```sql
WITH cte AS (
    SELECT e.id AS employee_id, e.name AS Employee, e.salary AS Salary, d.name AS Department
    FROM Employee e
    INNER JOIN Department d ON e.departmentId = d.id
),

wf AS (
    SELECT Department, Employee, Salary,
    DENSE_RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS r
    FROM cte
)

SELECT Department, Employee, Salary
FROM wf 
WHERE r = 1;
```

---

### Explanation of Query:

1. **CTE (Common Table Expression)**:
   - Joins `Employee` with `Department` using `departmentId` to fetch the department name for each employee.
   - Columns selected include `employee_id`, `Employee`, `Salary`, and `Department`.

2. **Window Function (wf)**:
   - Uses `DENSE_RANK` partitioned by `Department` and ordered by `Salary` in descending order to rank employees based on their salary within each department.

3. **Filter for Top Salaries**:
   - Filters rows where `r = 1`, indicating the highest salary in each department.

4. **Final Selection**:
   - Returns `Department`, `Employee`, and `Salary` columns for employees with the highest salaries in each department.
