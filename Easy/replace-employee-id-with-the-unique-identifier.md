# Replace Employee ID with the Unique Identifier

### Problem

#### Table: Employees

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| name          | varchar |

- `id` is the primary key of this table.
- This table contains information about the employees of a company.

#### Table: EmployeeUNI

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| unique_id     | int     |

- `(id, unique_id)` is the primary key of this table.
- This table contains information about employees and their corresponding unique IDs.

### Task

For each employee in the `Employees` table, retrieve their `unique_id` from the `EmployeeUNI` table. If an employee does not have a `unique_id`, return `null` for their `unique_id`. Return the result in any order.

### Example

#### Input

**Employees** table:

| id | name     |
|----|----------|
| 1  | Alice    |
| 7  | Bob      |
| 11 | Meir     |
| 90 | Winston  |
| 3  | Jonathan |

**EmployeeUNI** table:

| id | unique_id |
|----|-----------|
| 3  | 1         |
| 11 | 2         |
| 90 | 3         |

#### Output

| unique_id | name     |
|-----------|----------|
| null      | Alice    |
| null      | Bob      |
| 2         | Meir     |
| 3         | Winston  |
| 1         | Jonathan |

### Thought Process

To solve this problem, follow these steps:

1. **Understand the Relationship:**
   - The `Employees` table contains the employee details.
   - The `EmployeeUNI` table contains the unique IDs for some employees, linked by the `id` column.

2. **Join Tables:**
   - Use a `LEFT JOIN` to include all employees from the `Employees` table and match their `id` with the `EmployeeUNI` table.
   - A `LEFT JOIN` ensures that even if there is no match in the `EmployeeUNI` table, the employee still appears in the result, with `unique_id` as `null`.

3. **Select Relevant Columns:**
   - Retrieve the `unique_id` and `name` for each employee.

---

### Solution

```sql
SELECT eu.unique_id AS unique_id, e.name AS name
FROM Employees e
LEFT JOIN EmployeeUNI eu
ON eu.id = e.id;
```

---

### Solution Explanation

1. **LEFT JOIN:**
   - Combines the `Employees` table with the `EmployeeUNI` table using the `id` column.
   - Includes all rows from the `Employees` table, even if there is no match in the `EmployeeUNI` table.

2. **ON Clause:**
   - Matches rows from `Employees` with `EmployeeUNI` where `e.id = eu.id`.

3. **SELECT Statement:**
   - Retrieves the `unique_id` from `EmployeeUNI` and `name` from `Employees`.

4. **Null Handling:**
   - For employees without a matching row in `EmployeeUNI`, the `unique_id` will be `null`.

---

### Key Takeaways

- **LEFT JOIN:** Use this when you want to include all rows from the left table, even if there are no matches in the right table.
- **Null Values:** Handle cases where there is missing data by returning `null` for unmatched rows.
- **Aliasing:** Use aliases (`eu`, `e`) to make the query cleaner and easier to read.

---

### Related Topics

- SQL Joins
- Null Handling in SQL
- Data Retrieval from Multiple Tables
