
### Number of Calls Between Two Persons
**Table: Calls**

| Column Name | Type    |
|-------------|---------|
| from_id     | int     |
| to_id       | int     |
| duration    | int     |

- This table does not have a primary key (column with unique values), and it may contain duplicates.
- This table contains the duration of a phone call between `from_id` and `to_id`.
- It is guaranteed that `from_id != to_id`.

Write a solution to report the number of calls and the total call duration between each pair of distinct persons (`person1`, `person2`) where `person1 < person2`.

**Requirements:**
- `person1` and `person2` represent distinct users involved in a call.
- Ensure that the pair `person1, person2` is always ordered such that `person1 < person2`.
- Return the `call_count` (number of calls) and `total_duration` (sum of all call durations) for each pair.
- Return the result table in any order.

**Example Input:**

Calls table:

| from_id | to_id | duration |
|---------|-------|----------|
| 1       | 2     | 59       |
| 2       | 1     | 11       |
| 1       | 3     | 20       |
| 3       | 4     | 100      |
| 3       | 4     | 200      |
| 3       | 4     | 200      |
| 4       | 3     | 499      |

**Expected Output:**

| person1 | person2 | call_count | total_duration |
|---------|---------|------------|----------------|
| 1       | 2       | 2          | 70             |
| 1       | 3       | 1          | 20             |
| 3       | 4       | 4          | 999            |

---

### Thought Process

1. **Identify Unique Pairs:**
   - Each pair of users involved in a call should only appear once in the result table.
   - Use `LEAST()` and `GREATEST()` functions to consistently order `from_id` and `to_id` as `person1` and `person2`, respectively.

2. **Aggregate Data:**
   - Use the `GROUP BY` clause to group the calls by the unique pairs `person1` and `person2`.
   - Calculate the `call_count` (number of calls) and `total_duration` (sum of all call durations) for each pair.

3. **Output the Results:**
   - Return the aggregated results with the columns `person1`, `person2`, `call_count`, and `total_duration`.

4. **Order of Execution:**
   - Ensure that the `GROUP BY` clause groups correctly by the unique pair combination.
   - Use the `SUM()` function for total duration and the `COUNT(*)` function for the number of calls.

---

### SQL Query

```sql
SELECT 
    LEAST(from_id, to_id) AS person1,
    GREATEST(from_id, to_id) AS person2,
    COUNT(*) AS call_count,
    SUM(duration) AS total_duration
FROM Calls
GROUP BY 
    LEAST(from_id, to_id),
    GREATEST(from_id, to_id);
```

---

### Explanation

1. **LEAST and GREATEST:**
   - The `LEAST()` function ensures the smaller ID is always `person1`, while the `GREATEST()` function ensures the larger ID is always `person2`.
   - This consistent ordering prevents duplicate pairs (e.g., `1, 2` and `2, 1`).

2. **GROUP BY:**
   - Grouping by `LEAST(from_id, to_id)` and `GREATEST(from_id, to_id)` aggregates the calls for each unique pair of users.

3. **COUNT and SUM:**
   - The `COUNT(*)` function counts the number of calls for each pair.
   - The `SUM(duration)` function calculates the total duration of all calls for each pair.

4. **Result:**
   - The query returns the desired columns: `person1`, `person2`, `call_count`, and `total_duration`.

---
