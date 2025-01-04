# Primary Department for Each Employee

### Problem

#### Table: Employee

| Column Name   | Type    |
|---------------|---------|
| employee_id   | int     |
| department_id | int     |
| primary_flag  | varchar |

- `(employee_id, department_id)` is the primary key.
- `primary_flag` is an ENUM ('Y', 'N'):
  - 'Y' indicates the primary department for an employee.
  - 'N' indicates a non-primary department.
- An employee may belong to multiple departments. If they belong to only one department, the `primary_flag` will always be 'N'.

#### Task
Find the primary department for all employees. If an employee belongs to only one department, consider that department as their primary department.

### Example

#### Input

**Employee Table**:

| employee_id | department_id | primary_flag |
|-------------|---------------|--------------|
| 1           | 1             | N            |
| 2           | 1             | Y            |
| 2           | 2             | N            |
| 3           | 3             | N            |
| 4           | 2             | N            |
| 4           | 3             | Y            |
| 4           | 4             | N            |

#### Output

| employee_id | department_id |
|-------------|---------------|
| 1           | 1             |
| 2           | 1             |
| 3           | 3             |
| 4           | 3             |

#### Explanation
- Employee `1` belongs to only one department, so that department is primary.
- Employee `2` has a primary department explicitly marked as `Y`.
- Employee `3` belongs to only one department, so that department is primary.
- Employee `4` has a primary department explicitly marked as `Y`.

### Thought Process

1. **Identify Primary Departments**:
   - Use `primary_flag = 'Y'` to fetch explicitly marked primary departments.

2. **Handle Single-Department Employees**:
   - Identify employees belonging to only one department using `GROUP BY` and `HAVING`.

3. **Combine Results**:
   - Use an `OR` condition to fetch employees with either an explicitly marked primary department or only one department.

### Solution

```sql
SELECT employee_id, department_id
FROM Employee
WHERE primary_flag = 'Y'
   OR employee_id IN (
       SELECT employee_id
       FROM Employee
       GROUP BY employee_id
       HAVING COUNT(employee_id) = 1
   );
```

### Solution Explanation

1. **Primary Flag Check**:
   - `WHERE primary_flag = 'Y'` fetches rows where a department is explicitly marked as primary.

2. **Single-Department Employees**:
   - The subquery identifies employees with only one department using `GROUP BY` and `HAVING COUNT(employee_id) = 1`.

3. **Combine Results**:
   - Use `OR` to include both explicitly marked primary departments and single-department employees.

4. **Output**:
   - Returns the `employee_id` and `department_id` for all primary departments.

### Key Takeaways

- Use `GROUP BY` with `HAVING` to find groups meeting specific conditions (e.g., single-department employees).
- Combine conditions with `OR` to handle multiple scenarios in one query.
- This approach ensures the query is robust for employees with varying numbers of departments.

### Related Topics

- Conditional Logic in SQL
- GROUP BY and Aggregations
- Filtering with HAVING
