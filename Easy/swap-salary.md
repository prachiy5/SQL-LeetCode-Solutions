# Swap Salary

### Problem

#### Table: Salary

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |
| sex         | ENUM    |
| salary      | int     |

- `id` is the primary key.
- The `sex` column has ENUM values of type (`'m'`, `'f'`).
- The table contains information about employees, including their names, genders, and salaries.

#### Task
Write a single SQL `UPDATE` statement to swap all `'f'` and `'m'` values in the `sex` column:
- Change all `'m'` values to `'f'`.
- Change all `'f'` values to `'m'`.

#### Constraints
- Use a **single update statement**.
- Do **not** use any `SELECT` statement or temporary tables.

---

### Example

**Input:**

`Salary` table:

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |

**Output:**

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |

**Explanation:**
- Employee A (`id = 1`) and Employee C (`id = 3`) were changed from `'m'` to `'f'`.
- Employee B (`id = 2`) and Employee D (`id = 4`) were changed from `'f'` to `'m'`.

---

### Thought Process

1. **Understand the Problem:**
   - The goal is to swap the values in the `sex` column between `'m'` and `'f'`.

2. **Use a `CASE` Statement in an `UPDATE` Query:**
   - The `CASE` statement provides conditional logic within the `UPDATE` query.
   - If the current value is `'m'`, update it to `'f'`.
   - If the current value is `'f'`, update it to `'m'`.

3. **Avoid Temporary Tables:**
   - Perform the operation directly on the `Salary` table without creating any intermediate tables or using `SELECT` statements.

4. **Single Statement Requirement:**
   - Write the solution in one concise `UPDATE` query.

---

### Solution

```sql
# Write your MySQL query statement below
UPDATE Salary
SET sex = 
    CASE 
        WHEN sex = 'm' THEN 'f'
        WHEN sex = 'f' THEN 'm'
    END;
```

---

### Solution Explanation

1. **`UPDATE` Statement:**
   - Modifies the `sex` column of the `Salary` table.

2. **`CASE` Statement:**
   - Checks the current value of the `sex` column:
     - If `sex = 'm'`, it changes the value to `'f'`.
     - If `sex = 'f'`, it changes the value to `'m'`.

3. **Result:**
   - All `'m'` values are swapped with `'f'` and vice versa.

---

### Key Takeaways

- **`CASE` in `UPDATE`:** Use the `CASE` statement for conditional updates in SQL.
- **Direct Updates:** Perform operations directly on the table without intermediate steps when constraints require it.
- **Efficiency:** A single query ensures that the operation is efficient and meets the task requirements.

---

### Related Topics
- SQL Conditional Logic with `CASE`
- SQL `UPDATE` Queries
