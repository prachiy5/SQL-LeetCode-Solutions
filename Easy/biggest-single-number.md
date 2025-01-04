# Biggest Single Number

### Problem

#### Table: MyNumbers

| Column Name | Type |
|-------------|------|
| num         | int  |

- This table may contain duplicates.
- Each row represents an integer.

#### Task
A **single number** is a number that appears only once in the `MyNumbers` table. Find the largest single number.
- If there are no single numbers, return `null`.

---

### Example

**Input 1:**

`MyNumbers` table:

| num |
|-----|
| 8   |
| 8   |
| 3   |
| 3   |
| 1   |
| 4   |
| 5   |
| 6   |

**Output 1:**

| num |
|-----|
| 6   |

**Explanation:**
- Single numbers: `1`, `4`, `5`, `6`.
- The largest single number is `6`.

**Input 2:**

`MyNumbers` table:

| num |
|-----|
| 8   |
| 8   |
| 7   |
| 7   |
| 3   |
| 3   |
| 3   |

**Output 2:**

| num  |
|------|
| null |

**Explanation:**
- There are no single numbers, so the output is `null`.

---

### Thought Process

1. **Identify Single Numbers:**
   - Group the rows by `num`.
   - Use the `HAVING` clause to filter groups with a count of `1`.

2. **Find the Largest Single Number:**
   - Use the `MAX()` function to find the largest number among the single numbers.
   - Handle cases where there are no single numbers by returning `null`.

3. **Step-by-Step Approach:**
   - Use a Common Table Expression (CTE) to extract single numbers.
   - Apply the `MAX()` function on the CTE to find the largest single number.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT num
  FROM MyNumbers
  GROUP BY num
  HAVING COUNT(num) = 1
)

SELECT MAX(num) AS num
FROM cte;
```

---

### Solution Explanation

1. **CTE (`cte`):**
   - Groups the `MyNumbers` table by `num`.
   - Filters groups where the count of `num` is exactly `1` (single numbers).

2. **Main Query:**
   - Applies the `MAX()` function on the `cte` to find the largest single number.
   - If there are no rows in the `cte`, the result will automatically return `null`.

---

### Key Takeaways

- **GROUP BY with HAVING:** Useful for identifying specific conditions on grouped data.
- **CTEs:** Simplify complex queries by breaking them into smaller, manageable parts.
- **NULL Handling:** SQL automatically handles cases where aggregate functions like `MAX()` operate on empty sets, returning `null`.

---

### Related Topics
- SQL Aggregation Functions
- GROUP BY and HAVING
- Common Table Expressions (CTEs)
