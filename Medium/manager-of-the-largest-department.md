### Manager of the Largest Department

We are given a table:

#### Table: Employees
| Column Name | Type    |
|-------------|---------|
| emp_id      | int     |
| emp_name    | varchar |
| dep_id      | int     |
| position    | varchar |

- `emp_id` is the column of unique values.
- Each row contains:
  - `emp_id`: The ID of the employee.
  - `emp_name`: The name of the employee.
  - `dep_id`: The department ID where the employee works.
  - `position`: The position held by the employee.

#### Task
Write a query to find the name of the manager(s) from the largest department(s). A **largest department** is defined as the department(s) with the highest number of employees. If multiple departments have the same highest count, include the manager(s) from all of them.

Return the result table with the following columns:
- `manager_name`: The name of the manager.
- `dep_id`: The department ID.

The result should be ordered by `dep_id` in ascending order.

---

### Example Input/Output

##### Input:
**Employees table:**
| emp_id | emp_name  | dep_id | position      |
|--------|-----------|--------|---------------|
| 156    | Michael   | 107    | Manager       |
| 112    | Lucas     | 107    | Consultant    |
| 8      | Isabella  | 101    | Manager       |
| 160    | Joseph    | 100    | Manager       |
| 80     | Aiden     | 100    | Engineer      |
| 190    | Skylar    | 100    | Freelancer    |
| 196    | Stella    | 101    | Coordinator   |
| 167    | Audrey    | 100    | Consultant    |
| 97     | Nathan    | 101    | Supervisor    |
| 128    | Ian       | 101    | Administrator |
| 81     | Ethan     | 107    | Administrator |

##### Output:
| manager_name | dep_id |
|--------------|--------|
| Joseph       | 100    |
| Isabella     | 101    |

##### Explanation:
- **Department 100:**
  - Total employees: 4.
  - Manager: Joseph.
- **Department 101:**
  - Total employees: 4.
  - Manager: Isabella.
- **Department 107:**
  - Total employees: 3.
  - It is not the largest department.
- Since departments 100 and 101 have the highest number of employees, their managers are included in the output. The result is ordered by `dep_id` in ascending order.

---

### Thought Process
1. **Count Employees by Department:**
   - Use `COUNT(emp_id)` grouped by `dep_id` to determine the total number of employees in each department.
2. **Identify the Largest Department(s):**
   - Find the maximum employee count using `MAX()`.
   - Filter departments where the employee count equals the maximum.
3. **Find Managers in Largest Departments:**
   - Filter employees with the position `Manager` in the largest departments.
4. **Format and Order Results:**
   - Select `emp_name` (as `manager_name`) and `dep_id`.
   - Order the result by `dep_id` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT COUNT(emp_id) AS num_of_emp, dep_id
    FROM employees
    GROUP BY dep_id
),

cte2 AS (
    SELECT MAX(num_of_emp) AS m FROM cte
),

cte3 AS (
    SELECT dep_id FROM cte WHERE num_of_emp = (SELECT m FROM cte2)
),

cte4 AS (
    SELECT emp_name, dep_id, position
    FROM employees
    WHERE position = 'Manager'
)

SELECT emp_name AS manager_name, cte3.dep_id
FROM cte4
INNER JOIN cte3
ON cte3.dep_id = cte4.dep_id
ORDER BY dep_id;
```

---

### Key Takeaways
- **Counting and Grouping:**
  - Use `COUNT(emp_id)` to find the total number of employees per department.
- **Identifying Maximums:**
  - Use `MAX()` to find the largest department(s) based on employee count.
- **Filtering by Role:**
  - Filter managers using the `WHERE position = 'Manager'` condition.
- **Joining Results:**
  - Join the largest departments with the list of managers to get the desired output.
- **Ordering:**
  - Order the results by `dep_id` to meet the requirements.

---

### Related Topics
- SQL Aggregations (`COUNT`, `MAX`)
- Joins and Subqueries
- Filtering Rows with `WHERE`
- Ordering Results with `ORDER BY`
