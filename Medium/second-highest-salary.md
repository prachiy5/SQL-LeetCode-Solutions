### Second Highest Salary

Table: Employee

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
```

- `id` is the primary key (column with unique values) for this table.
- Each row of this table contains information about the salary of an employee.

Write a solution to find the second highest distinct salary from the Employee table. If there is no second highest salary, return `null` (or `None` in Pandas).

The result format is in the following example.

### Example 1:

**Input:**

Employee table:

```
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

**Output:**

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

### Example 2:

**Input:**

Employee table:

```
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+
```

**Output:**

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| null                |
+---------------------+
```

---

### Thought Process:

1. **Understand the Requirement:**
   - The task is to find the second-highest distinct salary.
   - If there is only one unique salary, the result should be `null`.

2. **Key Points:**
   - Use the `MAX` function to find the second-highest salary.
   - Ensure that the salary is distinct by filtering out duplicates.
   - Handle edge cases where there is only one or no salary present in the table.

3. **Approach:**
   - First, determine the highest salary using `MAX`.
   - Then, exclude the highest salary and use `MAX` again to find the second highest.
   - If no second highest exists, return `null`.

4. **SQL Concepts Used:**
   - `MAX()` function.
   - Subquery to exclude the highest salary.
   - Conditional handling for `null` results.

---

### Query:

```sql
SELECT MAX(salary) AS SecondHighestSalary
FROM Employee
WHERE salary < (SELECT MAX(salary) FROM Employee);
```

---

### Explanation:

1. **Subquery:**
   - `(SELECT MAX(salary) FROM Employee)` retrieves the highest salary from the `Employee` table.

2. **Filter Salaries:**
   - `WHERE salary < (SELECT MAX(salary) FROM Employee)` filters out the highest salary, leaving all other salaries.

3. **Find Second Highest:**
   - `MAX(salary)` is applied to the filtered salaries to find the second-highest distinct salary.

4. **Handle Null:**
   - If no second-highest salary exists (e.g., only one unique salary), the query returns `null`.

---

### Output Explanation:

**Example 1:**
- Salaries: `100`, `200`, `300`.
- Highest Salary: `300`.
- Second Highest Salary: `200`.

**Example 2:**
- Salaries: `100`.
- Highest Salary: `100`.
- Second Highest Salary: `null` (since there is only one unique salary).

---
