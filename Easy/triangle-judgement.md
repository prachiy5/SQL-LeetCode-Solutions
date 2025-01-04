# Triangle Judgement

### Problem

#### Table: Triangle

| Column Name | Type |
|-------------|------|
| x           | int  |
| y           | int  |
| z           | int  |

- `(x, y, z)` is the primary key.
- Each row contains the lengths of three line segments (`x`, `y`, and `z`).

#### Task
Determine whether the three line segments in each row can form a triangle. Return `Yes` if they can, and `No` otherwise.

**Triangle Formation Rule:**
For three segments to form a triangle:
1. The sum of any two sides must be greater than the third side:
   - `x + y > z`
   - `y + z > x`
   - `x + z > y`

#### Return Format
The result should include the original `x`, `y`, and `z` values along with a new column `triangle` that indicates whether the segments can form a triangle.

---

### Example

**Input:**

`Triangle` table:

| x  | y  | z  |
|----|----|----|
| 13 | 15 | 30 |
| 10 | 20 | 15 |

**Output:**

| x  | y  | z  | triangle |
|----|----|----|----------|
| 13 | 15 | 30 | No       |
| 10 | 20 | 15 | Yes      |

**Explanation:**
- For the first row (`13, 15, 30`):
  - `13 + 15 = 28`, which is not greater than `30`. So, it cannot form a triangle.
- For the second row (`10, 20, 15`):
  - All conditions are satisfied:
    - `10 + 20 > 15`
    - `20 + 15 > 10`
    - `10 + 15 > 20`
  - So, it forms a triangle.

---

### Thought Process

1. **Understand the Triangle Inequality Theorem:**
   - A triangle is valid if the sum of any two sides is greater than the third side.

2. **Write a SQL Query:**
   - Use a `CASE` statement to evaluate the triangle conditions for each row.
   - Check all three conditions (`x + y > z`, `y + z > x`, `x + z > y`) in the `CASE` statement.

3. **Return the Result:**
   - Include the original columns (`x`, `y`, `z`) and a new column `triangle` with values `Yes` or `No`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT x, y, z,
       CASE WHEN x + y > z AND y + z > x AND x + z > y THEN 'Yes'
            ELSE 'No' END AS triangle
FROM Triangle;
```

---

### Solution Explanation

- **Columns Selected:**
  - The query selects the original `x`, `y`, and `z` values.

- **CASE Statement:**
  - Evaluates whether the three sides satisfy the triangle inequality conditions:
    - `x + y > z`
    - `y + z > x`
    - `x + z > y`
  - If all conditions are true, the result is `Yes`. Otherwise, it is `No`.

- **Result Table:**
  - Includes the `triangle` column, which indicates whether the three segments form a triangle.

---

### Key Takeaways

- **Triangle Inequality Theorem:**
  - Critical for determining if three segments can form a triangle.

- **CASE Statements in SQL:**
  - Useful for applying conditional logic to each row in a table.

- **Column Selection:**
  - Always include relevant columns from the input table in the output for better context.

---

### Related Topics
- Conditional Logic with CASE in SQL
- Mathematical Constraints in SQL Queries
