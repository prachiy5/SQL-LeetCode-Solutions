# Consecutive Available Seats

### Problem

#### Table: Cinema

| Column Name | Type |
|-------------|------|
| seat_id     | int  |
| free        | bool |

- `seat_id` is an auto-increment column.
- Each row indicates whether the `seat_id` is free (`1`) or occupied (`0`).

#### Task
Find all the consecutive available seats in the cinema. Return the result table ordered by `seat_id` in ascending order.

**Note:**
- Test cases are generated such that there are always more than two consecutive available seats.

#### Example

**Input:**

`Cinema` table:

| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |

**Output:**

| seat_id |
|---------|
| 3       |
| 4       |
| 5       |

**Explanation:**
- Seats `3`, `4`, and `5` are all free and form a consecutive sequence.

---

### Thought Process

To solve this problem, the goal is to identify sequences of consecutive free seats. Here’s how I approached it step by step:

1. **Filter Free Seats:**
   - Use a `WHERE` clause to only include rows where `free = 1`.

2. **Identify Consecutive Sequences:**
   - Use the `ROW_NUMBER()` function to assign a sequential number to each row.
   - Subtract the `ROW_NUMBER()` value from the `seat_id` to create a grouping key. Consecutive seats will have the same grouping key.

3. **Filter Groups with More Than Two Seats:**
   - Use the `GROUP BY` and `HAVING` clauses to identify groups with more than two seats.

4. **Return the Result:**
   - Select the `seat_id` of seats belonging to these groups and order the result by `seat_id`.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT *,
         seat_id - ROW_NUMBER() OVER (ORDER BY seat_id) AS a
  FROM Cinema
  WHERE free = 1
)

SELECT seat_id
FROM cte
WHERE a IN (
    SELECT a
    FROM cte
    GROUP BY a
    HAVING COUNT(*) > 1
)
ORDER BY seat_id;
```

---

### Solution Explanation

1. **CTE (`cte`):**
   - Filters only rows where `free = 1`.
   - Calculates a grouping key (`a`) by subtracting the `ROW_NUMBER()` value from the `seat_id`. Consecutive seats will have the same value for `a`.

2. **Identify Groups:**
   - The subquery in the `WHERE` clause groups rows by `a` and counts the number of rows in each group.
   - Groups with `COUNT(*) > 1` indicate consecutive sequences of free seats.

3. **Final Selection:**
   - Selects the `seat_id` of rows belonging to groups with more than two seats.
   - Orders the result by `seat_id` in ascending order.

---

### Key Takeaways

- **Window Functions:** Use `ROW_NUMBER()` to generate sequential numbers for each row.
- **Grouping Keys:** Create a grouping key by combining `seat_id` with `ROW_NUMBER()` to identify consecutive sequences.
- **Filtering Groups:** Use `HAVING` to filter groups based on size.

---

### Related Topics
- Window Functions (`ROW_NUMBER`)
- Common Table Expressions (CTEs)
- Grouping and Filtering with `HAVING`
