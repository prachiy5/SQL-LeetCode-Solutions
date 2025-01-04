# Actors and Director who Cooperated at least three times

### Problem

#### Table: ActorDirector

| Column Name | Type |
|-------------|------|
| actor_id    | int  |
| director_id | int  |
| timestamp   | int  |

- `timestamp` is the primary key.
- Each row represents a collaboration between an actor (`actor_id`) and a director (`director_id`) at a specific time (`timestamp`).

#### Task
Find all pairs of `(actor_id, director_id)` where the actor has collaborated with the director at least three times.

---

### Example

**Input:**

`ActorDirector` table:

| actor_id | director_id | timestamp |
|----------|-------------|-----------|
| 1        | 1           | 0         |
| 1        | 1           | 1         |
| 1        | 1           | 2         |
| 1        | 2           | 3         |
| 1        | 2           | 4         |
| 2        | 1           | 5         |
| 2        | 1           | 6         |

**Output:**

| actor_id | director_id |
|----------|-------------|
| 1        | 1           |

**Explanation:**
- Actor `1` collaborated with Director `1` three times (timestamps `0`, `1`, `2`).
- Actor `1` collaborated with Director `2` two times, which is less than three.
- Actor `2` collaborated with Director `1` two times, which is less than three.

---

### Thought Process

1. **Group by Actor and Director:**
   - Use the `GROUP BY` clause to group rows by `(actor_id, director_id)`.

2. **Count Collaborations:**
   - Use the `COUNT(*)` function to count the number of collaborations for each pair.

3. **Filter by Minimum Collaborations:**
   - Use the `HAVING` clause to filter pairs with at least three collaborations (`COUNT(*) >= 3`).

4. **Return Results:**
   - Select the `actor_id` and `director_id` columns for the filtered pairs.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT actor_id, director_id
FROM ActorDirector
GROUP BY actor_id, director_id
HAVING COUNT(*) >= 3;
```

---

### Solution Explanation

1. **GROUP BY Clause:**
   - Groups rows by `(actor_id, director_id)` to calculate the number of collaborations for each pair.

2. **COUNT(*) Function:**
   - Counts the number of rows (collaborations) for each `(actor_id, director_id)` group.

3. **HAVING Clause:**
   - Filters out groups where the number of collaborations is less than three.

4. **SELECT Statement:**
   - Returns the `actor_id` and `director_id` for pairs meeting the criteria.

---

### Key Takeaways

- **GROUP BY and HAVING:** Use `GROUP BY` to aggregate data and `HAVING` to filter aggregated results.
- **COUNT Function:** Counts the number of rows in a group.
- **Filtering Collaborations:** The `HAVING` clause is essential for filtering based on aggregate functions like `COUNT()`.

---

### Related Topics
- SQL Aggregation Functions
- GROUP BY and HAVING
- Filtering Aggregated Data
