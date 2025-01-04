# Employees Earning More Than Their Managers

### Problem

#### Table: Employee

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |
| salary      | int     |
| managerId   | int     |

- `id` is the primary key (column with unique values) for this table.
- Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.

#### Task
Write a solution to find the employees who earn more than their managers.

Return the result table in any order.

#### Example

**Input:**

`Employee` table:

| id | name  | salary | managerId |
|----|-------|--------|-----------|
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | Null      |
| 4  | Max   | 90000  | Null      |

**Output:**

| Employee |
|----------|
| Joe      |

**Explanation:**
- Joe is the only employee who earns more than his manager.

---

### Thought Process

To solve this problem, we need to compare the salaries of employees with their respective managers. Here’s how I approached it:

1. **Understand the Table:**
   - Each row gives the employee's details (name, salary, and manager).
   - The `managerId` column links an employee to their manager (using the `id` column of the same table).

2. **Compare Salaries:**
   - To check if an employee earns more than their manager, we need to join the table with itself.
   - Match the employee's `managerId` with the `id` of their manager.

3. **Filter Results:**
   - Use a condition to filter rows where the employee's salary is greater than their manager's salary.

4. **Select Required Column:**
   - Only the `name` column of the employees who meet the condition is needed in the output.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT e.name AS Employee
FROM Employee e
JOIN Employee m
ON e.managerId = m.id
WHERE e.salary > m.salary;
```

---

### Solution Explanation

- **Self Join:**
  - The `Employee` table is joined with itself.
  - `e` is an alias for employees, and `m` is an alias for their managers.
- **Join Condition:**
  - `e.managerId = m.id` ensures that we match each employee with their manager.
- **Filter:**
  - `e.salary > m.salary` filters out employees earning more than their managers.
- **Select:**
  - The query selects the `name` of employees meeting the condition and renames the output column as `Employee`.

---

### Key Takeaways

- **Self Join:** This is a powerful technique to compare rows within the same table.
- **Alias Usage:** Aliases (`e` and `m`) make the query cleaner and easier to understand.
- **Filter Conditions:** Pay attention to the logic when comparing values.

---

### Related Topics
- Self Joins
- Filtering with WHERE clause
- Working with hierarchical data in SQL

