### Viewers Turned Streamers

Table: Sessions

| Column Name   | Type     |
|---------------|----------|
| user_id       | int      |
| session_start | datetime |
| session_end   | datetime |
| session_id    | int      |
| session_type  | enum     |

- `session_id` is a column of unique values for this table.
- `session_type` is an ENUM (category) type of (`Viewer`, `Streamer`).
- This table contains `user_id`, `session_start`, `session_end`, `session_id`, and `session_type`.

Write a solution to find the number of streaming sessions for users whose first session was as a viewer.

Return the result table ordered by count of streaming sessions and `user_id` in descending order.

---

### Example:

#### Input:

Sessions table:

| user_id | session_start       | session_end         | session_id | session_type |
|---------|---------------------|---------------------|------------|--------------|
| 101     | 2023-11-06 13:53:42 | 2023-11-06 14:05:42 | 375        | Viewer       |
| 101     | 2023-11-22 16:45:21 | 2023-11-22 20:39:21 | 594        | Streamer     |
| 102     | 2023-11-16 13:23:09 | 2023-11-16 16:10:09 | 777        | Streamer     |
| 102     | 2023-11-17 13:23:09 | 2023-11-17 16:10:09 | 778        | Streamer     |
| 101     | 2023-11-20 07:16:06 | 2023-11-20 08:33:06 | 315        | Streamer     |
| 104     | 2023-11-27 03:10:49 | 2023-11-27 03:30:49 | 797        | Viewer       |
| 103     | 2023-11-27 03:10:49 | 2023-11-27 03:30:49 | 798        | Streamer     |

#### Output:

| user_id | sessions_count |
|---------|----------------|
| 101     | 2              |

#### Explanation:
- `user_id` 101: Their first session was as a `Viewer` on 2023-11-06. They then had two `Streamer` sessions, so the count is 2.
- `user_id` 102: Their first session was as a `Streamer`, so they are excluded.
- `user_id` 103: They had only one session, which was as a `Streamer`, so they are excluded.
- `user_id` 104: Their first session was as a `Viewer`, but they did not have any `Streamer` sessions, so they are excluded.

---

### Thought Process:
1. **Rank Sessions by User**: Use `DENSE_RANK()` to identify the first session for each user based on the `session_start` time.
2. **Filter First Sessions**: Select users whose first session (`rn = 1`) was as a `Viewer`.
3. **Count Streamer Sessions**: For these users, count the number of sessions where `session_type` is `Streamer`.
4. **Sort the Result**: Order the output by the count of streaming sessions in descending order. If there are ties, sort by `user_id` in descending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        user_id,
        session_start,
        session_end,
        session_id,
        session_type,
        DENSE_RANK() OVER (PARTITION BY user_id ORDER BY session_start) AS rn
    FROM 
        sessions
),

viewers_first AS (
    SELECT 
        user_id,
        session_start,
        session_id,
        session_type
    FROM 
        cte 
    WHERE 
        rn = 1
        AND session_type = 'Viewer'
)

SELECT 
    user_id, 
    COUNT(session_type) AS sessions_count
FROM 
    cte 
WHERE 
    user_id IN (SELECT user_id FROM viewers_first)
    AND session_type = 'Streamer'
GROUP BY 
    user_id
ORDER BY 
    sessions_count DESC, 
    user_id DESC;
