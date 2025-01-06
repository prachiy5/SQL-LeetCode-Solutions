# Merge Overlapping Events in the Same Hall

## Table: HallEvents
| Column Name | Type |
|-------------|------|
| hall_id     | int  |
| start_day   | date |
| end_day     | date |

- This table may contain duplicate rows.
- Each row indicates the start day and end day of an event and the hall in which the event is held.

## Task
Write a solution to merge all the overlapping events that are held in the same hall. Two events overlap if they have at least one day in common.

Return the result table in any order.

---

# Example

### Input

#### HallEvents Table:
| hall_id | start_day  | end_day    |
|---------|------------|------------|
| 1       | 2023-01-13 | 2023-01-14 |
| 1       | 2023-01-14 | 2023-01-17 |
| 1       | 2023-01-18 | 2023-01-25 |
| 2       | 2022-12-09 | 2022-12-23 |
| 2       | 2022-12-13 | 2022-12-17 |
| 3       | 2022-12-01 | 2023-01-30 |

### Output

| hall_id | start_day  | end_day    |
|---------|------------|------------|
| 1       | 2023-01-13 | 2023-01-17 |
| 1       | 2023-01-18 | 2023-01-25 |
| 2       | 2022-12-09 | 2022-12-23 |
| 3       | 2022-12-01 | 2023-01-30 |

### Explanation
- **Hall 1**:
  - Events [2023-01-13, 2023-01-14] and [2023-01-14, 2023-01-17] overlap. They are merged into [2023-01-13, 2023-01-17].
  - Event [2023-01-18, 2023-01-25] does not overlap with any other event, so it remains as it is.
- **Hall 2**:
  - Events [2022-12-09, 2022-12-23] and [2022-12-13, 2022-12-17] overlap. They are merged into [2022-12-09, 2022-12-23].
- **Hall 3**:
  - There is only one event in this hall, so it remains unchanged.

---

# Thought Process

1. **Remove Duplicates**:
   - The table may contain duplicate rows, so eliminate duplicates first.

2. **Order Events**:
   - Assign a row number (`rn`) to each event, ordered by `hall_id` and `start_day`.

3. **Recursive Merge**:
   - Use a recursive common table expression (CTE) to merge overlapping events.
   - For each event, check if its `start_day` overlaps with the `end_day` of the previous event in the same hall.
   - If they overlap, merge the events by taking the earlier `start_day` and the later `end_day`.

4. **Finalize Merged Events**:
   - Use a `GROUP BY` query to consolidate the results, ensuring that overlapping events are combined into single rows.

---

# Solution

```sql
WITH RECURSIVE cte AS (
    SELECT *,
        ROW_NUMBER() OVER (ORDER BY hall_id, start_day) AS rn 
    FROM (SELECT DISTINCT * FROM HallEvents) h
),
merge_cte AS (
    SELECT
        hall_id,
        start_day,
        end_day,
        rn
    FROM cte r
    WHERE r.rn = 1

    UNION

    SELECT
        r.hall_id,
        CASE WHEN r.hall_id = m.hall_id AND r.start_day <= m.end_day 
            THEN m.start_day ELSE r.start_day
        END AS start_day,
        CASE WHEN r.hall_id = m.hall_id AND r.end_day <= m.end_day
            THEN m.end_day ELSE r.end_day
        END AS end_day,
        r.rn
    FROM cte r 
    JOIN merge_cte m 
    ON r.rn = m.rn + 1
    WHERE r.rn <= (SELECT MAX(rn) FROM cte)  
)
SELECT
    hall_id,
    start_day,
    MAX(end_day) AS end_day
FROM merge_cte
GROUP BY hall_id, start_day;
```

---

# Solution Explanation

1. **Remove Duplicates and Assign Row Numbers (`cte`)**:
   - Eliminate duplicate rows.
   - Assign row numbers to events, ordered by `hall_id` and `start_day`.

2. **Recursive Merge (`merge_cte`)**:
   - Start with the first event (`rn = 1`).
   - For each subsequent event:
     - If the `start_day` overlaps with the `end_day` of the previous event, merge the events by adjusting `start_day` and `end_day`.
     - If no overlap, keep the event as it is.

3. **Group and Consolidate Results**:
   - Use `GROUP BY` to combine overlapping events into single rows.
   - Return the final merged intervals for each hall.

---

# Key Takeaways
- Recursive queries are useful for merging overlapping intervals.
- Window functions like `ROW_NUMBER()` help in ordering and assigning unique IDs to rows.
- Eliminating duplicates is essential before performing operations on the data.

---

# Related Topics
- Common Table Expressions (CTEs)
- Recursive Queries
- Window Functions (`ROW_NUMBER`)
- Grouping and Aggregation
