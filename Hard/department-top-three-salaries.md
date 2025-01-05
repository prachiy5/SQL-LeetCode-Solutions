# Department Top Three Salaries
You are given two tables:

### Employee Table
| Column Name  | Type    |
|--------------|---------|
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

- `id` is the primary key (column with unique values) for this table.
- `departmentId` is a foreign key (reference column) of the ID from the Department table.
- Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

### Department Table
| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key (column with unique values) for this table.
- Each row of this table indicates the ID of a department and its name.

### Task
The company's executives are interested in seeing who earns the most money in each of the company's departments. A **high earner** in a department is an employee who has a salary in the **top three unique salaries** for that department.

Write a solution to find the employees who are high earners in each of the departments.

Return the result table in any order.

### Input Example
#### Employee Table:
| id | name  | salary | departmentId |
|----|-------|--------|--------------|
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

#### Department Table:
| id | name  |
|----|-------|
| 1  | IT    |
| 2  | Sales |

### Output Example
| Department | Employee | Salary |
|------------|----------|--------|
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

### Explanation

1. **IT Department**:
   - Max earns the highest unique salary (`90000`).
   - Both Randy and Joe earn the second-highest unique salary (`85000`).
   - Will earns the third-highest unique salary (`70000`).

2. **Sales Department**:
   - Henry earns the highest unique salary (`80000`).
   - Sam earns the second-highest unique salary (`60000`).
   - There is no third-highest salary as there are only two employees.

### Constraints

There are no employees with the exact same name, salary, and department.

### Thought Process
1. **Join Tables**:
   - Use an `INNER JOIN` to combine the `Employee` and `Department` tables on the `departmentId` column.

2. **Rank Salaries Within Each Department**:
   - Use the `DENSE_RANK()` function to rank unique salaries in descending order for each department.

3. **Filter for Top Three Salaries**:
   - Select rows where the rank is `1`, `2`, or `3` for the top three unique salaries in each department.

4. **Select Required Columns**:
   - Include the department name, employee name, and salary in the result.

### Query
```sql
WITH RankedSalaries AS (
    SELECT 
        d.name AS Department, 
        e.name AS Employee, 
        e.salary,
        DENSE_RANK() OVER (PARTITION BY e.departmentId ORDER BY e.salary DESC) AS salary_rank
    FROM 
        Employee e
    INNER JOIN 
        Department d
    ON 
        e.departmentId = d.id
)

SELECT 
    Department, 
    Employee, 
    Salary
FROM 
    RankedSalaries
WHERE 
    salary_rank <= 3;
