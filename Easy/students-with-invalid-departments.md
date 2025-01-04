# Students with Invalid Departments

### Problem

#### Table: Departments

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| name          | varchar |

- `id` is the primary key of this table.
- This table contains information about university departments.

#### Table: Students

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| name          | varchar |
| department_id | int     |

- `id` is the primary key of this table.
- This table contains information about students and their associated department IDs.

### Task

Find the `id` and `name` of all students who are enrolled in departments that no longer exist (departments not listed in the `Departments` table). Return the result in any order.

### Example

#### Input

**Departments** table:

| id   | name                     |
|------|--------------------------|
| 1    | Electrical Engineering   |
| 7    | Computer Engineering     |
| 13   | Business Administration  |

**Students** table:

| id   | name     | department_id |
|------|----------|---------------|
| 23   | Alice    | 1             |
| 1    | Bob      | 7             |
| 5    | Jennifer | 13            |
| 2    | John     | 14            |
| 4    | Jasmine  | 77            |
| 3    | Steve    | 74            |
| 6    | Luis     | 1             |
| 8    | Jonathan | 7             |
| 7    | Daiana   | 33            |
| 11   | Madelynn | 1             |

#### Output

| id   | name     |
|------|----------|
| 2    | John     |
| 7    | Daiana   |
| 4    | Jasmine  |
| 3    | Steve    |

### Thought Process

To solve this problem, follow these steps:

1. **Identify Nonexistent Departments:**
   - Use the `Departments` table to find all valid department IDs.
   - Any `department_id` in the `Students` table that is not in the `Departments` table means the department no longer exists.

2. **Filter Students:**
   - Use a `WHERE` clause with `NOT IN` to filter students whose `department_id` is not in the `Departments` table.

3. **Select Required Columns:**
   - Retrieve the `id` and `name` of the filtered students.

---

### Solution

```sql
SELECT id, name
FROM Students s
WHERE department_id NOT IN (
    SELECT id FROM Departments
);
```

---

### Solution Explanation

1. **Subquery:**
   - The subquery `SELECT id FROM Departments` retrieves all valid department IDs.

2. **NOT IN Clause:**
   - Filters rows in the `Students` table where `department_id` does not match any valid department IDs from the `Departments` table.

3. **SELECT Statement:**
   - Outputs the `id` and `name` columns for students enrolled in nonexistent departments.

---

### Key Takeaways

- **Subqueries:** Use subqueries to filter data based on conditions involving another table.
- **NOT IN Operator:** Useful for identifying rows where a column value does not exist in a set of values.
- **SQL Join Alternative:** While `NOT IN` is effective, you can also solve this problem using a `LEFT JOIN` with `NULL` checks.

---

### Related Topics

- SQL Subqueries
- Filtering with NOT IN
- Alternative Solutions with Joins
