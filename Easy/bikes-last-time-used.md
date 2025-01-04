### Bikes Last Time Used
**Table: Bikes**

```plaintext
+-------------+----------+ 
| Column Name | Type     | 
+-------------+----------+ 
| ride_id     | int      | 
| bike_number | int      | 
| start_time  | datetime |
| end_time    | datetime |
+-------------+----------+
```
- `ride_id` column contains unique values.
- Each row contains ride information that includes `ride_id`, `bike_number`, `start_time`, and `end_time` of the ride.
- It is guaranteed that `start_time` and `end_time` are valid datetime values.

Write a solution to find the last time when each bike was used.

Return the result table ordered by the bikes that were most recently used. 

The result format is in the following example.

---

### Example:

**Input:**

**Bikes table:**

```plaintext
+---------+-------------+---------------------+---------------------+ 
| ride_id | bike_number | start_time          | end_time            |  
+---------+-------------+---------------------+---------------------+
| 1       | W00576      | 2012-03-25 11:30:00 | 2012-03-25 12:40:00 |
| 2       | W00300      | 2012-03-25 10:30:00 | 2012-03-25 10:50:00 |
| 3       | W00455      | 2012-03-26 14:30:00 | 2012-03-26 17:40:00 |
| 4       | W00455      | 2012-03-25 12:30:00 | 2012-03-25 13:40:00 |
| 5       | W00576      | 2012-03-25 08:10:00 | 2012-03-25 09:10:00 |
| 6       | W00576      | 2012-03-28 02:30:00 | 2012-03-28 02:50:00 |
+---------+-------------+---------------------+---------------------+ 
```

**Output:**

```plaintext
+-------------+---------------------+ 
| bike_number | end_time            |  
+-------------+---------------------+
| W00576      | 2012-03-28 02:50:00 |
| W00455      | 2012-03-26 17:40:00 |
| W00300      | 2012-03-25 10:50:00 |
+-------------+---------------------+ 
```

**Explanation:**
- Bike `W00576` has three rides. The most recent ride is `ride_id` 6, which ended on `2012-03-28 02:50:00`.
- Bike `W00300` has only one ride, so its `end_time` is directly included in the output.
- Bike `W00455` has two rides. The most recent ride is `ride_id` 3, which ended on `2012-03-26 17:40:00`.
- The result is ordered by the most recent usage (`end_time DESC`).

---

### Thought Process:
1. **Understand the Requirement:**
   - For each bike (`bike_number`), find its most recent ride (`end_time`).
   - The output should be ordered by the most recent `end_time` in descending order.

2. **Key Insights:**
   - Use a **window function** (`DENSE_RANK`) to rank rides by `end_time` for each `bike_number`.
   - Filter only the rides with the most recent `end_time` (where `rank = 1`).
   - Order the final output by `end_time DESC`.

3. **Steps to Solve:**
   - Use a Common Table Expression (CTE) or subquery to calculate the `DENSE_RANK` for each ride based on `end_time` within each `bike_number`.
   - Select only rows with `rank = 1`.
   - Order the results by `end_time DESC` to get the most recently used bikes at the top.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        ride_id, 
        bike_number, 
        start_time, 
        end_time,
        DENSE_RANK() OVER (PARTITION BY bike_number ORDER BY end_time DESC) AS rn
    FROM 
        bikes
)

SELECT 
    bike_number, 
    end_time 
FROM 
    cte 
WHERE 
    rn = 1
ORDER BY 
    end_time DESC;
```

---

### Explanation of the Query:
1. **Step 1: Calculate Ranks with `DENSE_RANK`:**
   - The `DENSE_RANK` function assigns a rank to each `end_time` within each `bike_number`. The most recent `end_time` gets the rank of `1`.

2. **Step 2: Filter Most Recent Rides:**
   - In the `WHERE` clause, select rows where the rank (`rn`) is `1`. This ensures we only consider the most recent ride for each bike.

3. **Step 3: Order Results:**
   - The `ORDER BY end_time DESC` ensures the output is sorted by the most recent usage across all bikes.

---

### Why This Output?
- **`W00576`**: Most recent `end_time` is `2012-03-28 02:50:00`.
- **`W00455`**: Most recent `end_time` is `2012-03-26 17:40:00`.
- **`W00300`**: Only one ride, so `end_time` is `2012-03-25 10:50:00`.
- Output is ordered by these times in descending order.

---

### Final Output:

```plaintext
+-------------+---------------------+ 
| bike_number | end_time            |  
+-------------+---------------------+
| W00576      | 2012-03-28 02:50:00 |
| W00455      | 2012-03-26 17:40:00 |
| W00300      | 2012-03-25 10:50:00 |
+-------------+---------------------+ 
