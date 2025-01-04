# Employees with Bonus Less Than 1000

### Problem

#### Table: Employee

| Column Name | Type    |
|-------------|---------|
| empId       | int     |
| name        | varchar |
| supervisor  | int     |
| salary      | int     |

- `empId` is the column with unique values for this table.
- Each row indicates the name and the ID of an employee, their salary, and the ID of their manager (supervisor).

#### Table: Bonus

| Column Name | Type |
|-------------|------|
| empId       | int  |
| bonus       | int  |

- `empId` is the column of unique values for this table.
- `empId` is a foreign key referencing `empId` in the `Employee` table.
- Each row contains the ID of an employee and their respective bonus amount.

#### Task
Write a solution to report the `name` and `bonus` amount of each employee who has a bonus less than 1000. If an employee does not have a bonus, their bonus should be displayed as `NULL`.

Return the result table in any order.

#### Example

**Input:**

`Employee` table:

| empId | name   | supervisor | salary |
|-------|--------|------------|--------|
| 3     | Brad   | null       | 4000   |
| 1     | John   | 3          | 1000   |
| 2     | Dan    | 3          | 2000   |
| 4     | Thomas | 3          | 4000   |

`Bonus` table:

| empId | bonus |
|-------|-------|
| 2     | 500   |
| 4     | 2000  |

**Output:**

```
| name | bonus |
|------+-------|
| Brad | null  |
| John | null  |
| Dan  | 500   |
```

**Explanation:**
- Brad and John do not have bonuses, so their `bonus` is displayed as `NULL`.
- Dan has a bonus of 500, which is less than 1000, so he is included in the output.
- Thomas has a bonus of 2000, so he is excluded from the output.

---

### Thought Process

To solve this problem, the goal is to combine data from the `Employee` and `Bonus` tables to identify employees with a bonus less than 1000 or no bonus at all. Here’s how I approached it step by step:

1. **Understand the Relationships:**
   - The `empId` column links the `Employee` and `Bonus` tables.
   - An employee may or may not have an entry in the `Bonus` table.

2. **Combine the Tables:**
   - Use a `LEFT JOIN` to include all employees from the `Employee` table, even if they do not have a bonus in the `Bonus` table.

3. **Filter for Bonus Conditions:**
   - Use the `WHERE` clause to filter rows where:
     - The bonus is less than 1000, or
     - The bonus is `NULL` (indicating no bonus).

4. **Select Required Columns:**
   - Return the `name` and `bonus` of the employees meeting the criteria.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

---

### Solution Explanation

- **LEFT JOIN:**
  - Combines rows from the `Employee` table with rows from the `Bonus` table based on the `empId`.
  - Ensures all employees from the `Employee` table are included, even if they have no bonus.

- **Filter Conditions:**
  - `b.bonus < 1000`: Includes employees with a bonus less than 1000.
  - `b.bonus IS NULL`: Includes employees with no bonus.

- **Select Statement:**
  - Retrieves the `name` and `bonus` of the employees meeting the conditions.

---

### Key Takeaways

- **LEFT JOIN:** Useful for combining data from two tables while ensuring all rows from one table are included.
- **Handling NULL Values:** Use `IS NULL` to identify rows with no matching data in the joined table.
- **Filtering with Multiple Conditions:** Combine conditions using `OR` for flexible filtering logic.

---

### Related Topics
- Joins in SQL
- Filtering with WHERE Clause
- Handling NULL Values in SQL
