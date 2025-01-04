### Find Latest Salaries

**Table:** Salary

```
+---------------+---------+ 
| Column Name   | Type    | 
+---------------+---------+ 
| emp_id        | int     | 
| firstname     | varchar |
| lastname      | varchar |
| salary        | varchar |
| department_id | varchar |
+---------------+---------+
```

`(emp_id, salary)` is the primary key (combination of columns with unique values) for this table.
Each row contains employee details and their yearly salaries. However, some of the records are old and contain outdated salary information. 

Write a solution to find the current salary of each employee, assuming that salaries increase each year. Output their `emp_id`, `firstname`, `lastname`, `salary`, and `department_id`.

Return the result table ordered by `emp_id` in ascending order.

### Example:

**Input:**

**Salary table:**

```
+--------+-----------+----------+--------+---------------+
| emp_id | firstname | lastname | salary | department_id |
+--------+-----------+----------+--------+---------------+ 
| 1      | Todd      | Wilson   | 110000 | D1006         |
| 1      | Todd      | Wilson   | 106119 | D1006         | 
| 2      | Justin    | Simon    | 128922 | D1005         | 
| 2      | Justin    | Simon    | 130000 | D1005         | 
| 3      | Kelly     | Rosario  | 42689  | D1002         | 
| 4      | Patricia  | Powell   | 162825 | D1004         |
| 4      | Patricia  | Powell   | 170000 | D1004         |
| 5      | Sherry    | Golden   | 44101  | D1002         | 
| 6      | Natasha   | Swanson  | 79632  | D1005         | 
| 6      | Natasha   | Swanson  | 90000  | D1005         |
+--------+-----------+----------+--------+---------------+
```

**Output:**

```
+--------+-----------+----------+--------+---------------+
| emp_id | firstname | lastname | salary | department_id |
+--------+-----------+----------+--------+---------------+ 
| 1      | Todd      | Wilson   | 110000 | D1006         |
| 2      | Justin    | Simon    | 130000 | D1005         | 
| 3      | Kelly     | Rosario  | 42689  | D1002         | 
| 4      | Patricia  | Powell   | 170000 | D1004         |
| 5      | Sherry    | Golden   | 44101  | D1002         | 
| 6      | Natasha   | Swanson  | 90000  | D1005         |
+--------+-----------+----------+--------+---------------+
```

### Explanation:

- `emp_id 1`: The most recent salary is `110000`.
- `emp_id 2`: The most recent salary is `130000`.
- `emp_id 3`: Only one record, so the salary remains `42689`.
- `emp_id 4`: The most recent salary is `170000`.
- `emp_id 5`: Only one record, so the salary remains `44101`.
- `emp_id 6`: The most recent salary is `90000`.

---

### Thought Process:

1. **Identify the Problem:**
   - Each employee may have multiple salary records, but we need only the latest (highest) salary for each employee.
   
2. **Analyze the Data:**
   - Use the `emp_id` to group salaries for each employee.
   - The highest salary per `emp_id` represents the current salary.

3. **Approach:**
   - Use a **common table expression (CTE)** to rank salaries in descending order for each `emp_id`.
   - Use the `DENSE_RANK()` window function to rank salaries and select the latest (rank = 1).

4. **Output Requirements:**
   - Return `emp_id`, `firstname`, `lastname`, `salary`, and `department_id`.
   - Order the results by `emp_id` in ascending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        emp_id,
        firstname,
        lastname,
        salary,
        department_id,
        DENSE_RANK() OVER (PARTITION BY emp_id ORDER BY salary DESC) AS rank
    FROM 
        Salary
)

SELECT 
    emp_id, 
    firstname, 
    lastname, 
    salary, 
    department_id
FROM 
    cte
WHERE 
    rank = 1
ORDER BY 
    emp_id;
```

---

### Explanation of the Query:

1. **CTE (Common Table Expression):**
   - Partition rows by `emp_id`.
   - Use `DENSE_RANK()` to rank salaries in descending order.
   - Assign rank `1` to the highest salary for each `emp_id`.

2. **Filter Highest Salaries:**
   - Select only rows where `rank = 1`, which represent the latest salary for each employee.

3. **Ordering Results:**
   - Sort the final result by `emp_id` in ascending order.

---

### Why This Works:
- The `DENSE_RANK()` function ensures that ties in salary are handled appropriately.
- Filtering by `rank = 1` isolates the highest salary for each employee.
- The final output meets all the requirements, including sorting by `emp_id`.


