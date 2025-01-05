### Consecutive Numbers
**Table:** Logs

```plaintext
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
```
- `id` is the primary key for this table.
- `id` is an autoincrement column starting from 1.

Find all numbers that appear at least three times consecutively.

**Return the result table in any order.**

**Example:**

**Input:**

Logs table:

```plaintext
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+
```

**Output:**

```plaintext
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

**Explanation:**
- `1` is the only number that appears consecutively for at least three times.

---

### Thought Process

1. **Understand the Problem:** We need to identify numbers that appear three or more times in a row in the `Logs` table.
   - Consecutive numbers mean the same value in the `num` column appears in three rows with increasing `id` values.

2. **Key Points:**
   - Use the `id` column to determine consecutive rows.
   - Check if the `num` values in three consecutive rows are the same.

3. **Approach:**
   - Join the `Logs` table with itself three times to compare consecutive rows.
   - Use conditions to ensure that the `id` values differ by 1.
   - Filter rows where the `num` values in these three rows are the same.
   - Use `DISTINCT` to remove duplicate results.

4. **Output:**
   - Return the distinct `num` values as `ConsecutiveNums`.

---

### Query

```sql
SELECT DISTINCT l1.num AS ConsecutiveNums 
FROM logs l1 
JOIN logs l2 ON l1.id = l2.id - 1 
JOIN logs l3 ON l2.id = l3.id - 1
WHERE l1.num = l2.num AND l1.num = l3.num;
```

---

### Explanation of the Query

1. **Table Aliases:**
   - `l1`, `l2`, and `l3` represent three consecutive rows from the `Logs` table.

2. **Joins:**
   - `l2.id = l1.id + 1`: Ensures `l2` is the row immediately after `l1`.
   - `l3.id = l2.id + 1`: Ensures `l3` is the row immediately after `l2`.

3. **Filter Condition:**
   - `l1.num = l2.num AND l1.num = l3.num`: Checks if the `num` values are the same in all three rows.

4. **Distinct Values:**
   - `DISTINCT`: Ensures that duplicate entries for the same `num` value are removed.

5. **Result:**
   - Returns a list of numbers that appear at least three times consecutively.

---

### Why This Works
- The query uses self-joins to compare consecutive rows based on the `id` column.
- The condition `l1.num = l2.num AND l1.num = l3.num` ensures that only numbers repeated three times consecutively are included.
- `DISTINCT` removes duplicates to provide the final result as unique values.

---

### Output for Example Input

Given the example input, the query correctly identifies `1` as the only number that appears consecutively at least three times.
