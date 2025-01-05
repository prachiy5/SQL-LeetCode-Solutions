### Group Employees of the Same Salary

**Table: Employees**

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
| salary      | int     |
+-------------+---------+
```
- `employee_id` is the column with unique values for this table.
- Each row of this table indicates the employee ID, employee name, and salary.

A company wants to divide the employees into teams such that all the members on each team have the same salary. The teams should follow these criteria:

1. Each team should consist of at least two employees.
2. All the employees on a team should have the same salary.
3. All the employees of the same salary should be assigned to the same team.
4. If the salary of an employee is unique, we do not assign this employee to any team.
5. A team's ID is assigned based on the rank of the team's salary relative to the other teams' salaries, where the team with the lowest salary has `team_id = 1`. Salaries for employees not on a team are not included in this ranking.

**Objective:**

Write a solution to get the `team_id` of each employee that is in a team.

**Return the result table ordered by:**
1. `team_id` in ascending order
2. In case of a tie, `employee_id` in ascending order.

### Example 1:

**Input:**

**Employees table:**

```
+-------------+---------+--------+
| employee_id | name    | salary |
+-------------+---------+--------+
| 2           | Meir    | 3000   |
| 3           | Michael | 3000   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7400   |
+-------------+---------+--------+
```

**Output:**

```
+-------------+---------+--------+---------+
| employee_id | name    | salary | team_id |
+-------------+---------+--------+---------+
| 2           | Meir    | 3000   | 1       |
| 3           | Michael | 3000   | 1       |
| 7           | Addilyn | 7400   | 2       |
| 9           | Kannon  | 7400   | 2       |
+-------------+---------+--------+---------+
```

**Explanation:**
- Meir (`employee_id=2`) and Michael (`employee_id=3`) are in the same team because they have the same salary of 3000.
- Addilyn (`employee_id=7`) and Kannon (`employee_id=9`) are in the same team because they have the same salary of 7400.
- Juan (`employee_id=8`) is not included in any team because their salary of 6100 is unique (no other employee has the same salary).

The `team_id` values are assigned as follows:
- `team_id=1`: Meir and Michael, salary = 3000.
- `team_id=2`: Addilyn and Kannon, salary = 7400.

Juan’s salary of 6100 is not included in the ranking because they are not on a team.

---

### Thought Process:

1. **Filter employees who are eligible for teams:**
   - Use a `COUNT(*) OVER(PARTITION BY salary)` to count the number of employees with the same salary.
   - Include only those salaries where the count is 2 or more.

2. **Assign `team_id` to grouped salaries:**
   - Use `DENSE_RANK()` to rank the salaries of eligible teams in ascending order, starting from the lowest salary as `team_id=1`.

3. **Format the output:**
   - Return the `employee_id`, `name`, `salary`, and `team_id`.
   - Ensure the result is ordered by `team_id` and then `employee_id`.

---

### Query:

```sql
WITH cte AS (
    SELECT employee_id, name, salary,
           COUNT(*) OVER (PARTITION BY salary) AS salary_count
    FROM employees
)
SELECT employee_id, name, salary,
       DENSE_RANK() OVER (ORDER BY salary) AS team_id
FROM cte
WHERE salary_count >= 2
ORDER BY team_id, employee_id;
```

---

### Explanation of the Query:

1. **Step 1 - Common Table Expression (CTE):**
   - `COUNT(*) OVER (PARTITION BY salary)` calculates the number of employees for each salary.
   - The CTE filters out salaries that appear only once (`salary_count >= 2`).

2. **Step 2 - Assign Team IDs:**
   - Use `DENSE_RANK()` to assign team IDs based on salary ranking in ascending order.
   - The `DENSE_RANK()` ensures consecutive IDs for valid teams.

3. **Step 3 - Order and Output:**
   - The result is ordered by `team_id` and then by `employee_id` for a consistent format.

---

### Output:

The query provides the desired result, as demonstrated in the example, listing employees in valid teams with their respective `team_id`.
