### User Activities within Time Bounds

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

Write a solution to find the users who have had at least one consecutive session of the same type (either `Viewer` or `Streamer`) with a maximum gap of 12 hours between sessions.

Return the result table ordered by `user_id` in ascending order.

---

### Example:

#### Input:

Sessions table:

| user_id | session_start       | session_end         | session_id | session_type |
|---------|---------------------|---------------------|------------|--------------|
| 101     | 2023-11-01 08:00:00 | 2023-11-01 09:00:00 | 1          | Viewer       |
| 101     | 2023-11-01 10:00:00 | 2023-11-01 11:00:00 | 2          | Streamer     |
| 102     | 2023-11-01 13:00:00 | 2023-11-01 14:00:00 | 3          | Viewer       |
| 102     | 2023-11-01 15:00:00 | 2023-11-01 16:00:00 | 4          | Viewer       |
| 101     | 2023-11-02 09:00:00 | 2023-11-02 10:00:00 | 5          | Viewer       |
| 102     | 2023-11-02 12:00:00 | 2023-11-02 13:00:00 | 6          | Streamer     |
| 101     | 2023-11-02 13:00:00 | 2023-11-02 14:00:00 | 7          | Streamer     |
| 102     | 2023-11-02 16:00:00 | 2023-11-02 17:00:00 | 8          | Viewer       |
| 103     | 2023-11-01 08:00:00 | 2023-11-01 09:00:00 | 9          | Viewer       |
| 103     | 2023-11-02 20:00:00 | 2023-11-02 23:00:00 | 10         | Viewer       |
| 103     | 2023-11-03 09:00:00 | 2023-11-03 10:00:00 | 11         | Viewer       |

#### Output:

| user_id |
|---------|
| 102     |
| 103     |

#### Explanation:
- User ID `101` will not be included in the final output as they do not have any consecutive sessions of the same session type.
- User ID `102` will be included in the final output as they had two `Viewer` sessions with session IDs 3 and 4, respectively, and the time gap between them was less than 12 hours.
- User ID `103` participated in two `Viewer` sessions with a gap of less than 12 hours between them, identified by session IDs 10 and 11. Therefore, user 103 will be included in the final output.

The output table is ordered by `user_id` in ascending order.

---

### Thought Process:
1. **Identify Consecutive Sessions**:
   - Use the `LEAD()` window function to find the `session_start` time of the next session for the same `user_id` and `session_type`.
2. **Calculate Time Gaps**:
   - Use `TIMESTAMPDIFF()` to compute the gap (in hours) between the `session_end` of the current session and the `session_start` of the next session.
3. **Filter Valid Sessions**:
   - Select users whose sessions have a gap of 12 hours or less and ensure the session types match.
4. **Remove Duplicates**:
   - Use `DISTINCT` to ensure each user appears only once in the result.
5. **Order Results**:
   - Sort the final output by `user_id` in ascending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        *,
        LEAD(session_start) OVER (PARTITION BY user_id, session_type ORDER BY session_start ASC) AS next_session_start
    FROM 
        sessions
)

SELECT DISTINCT 
    user_id
FROM 
    cte
WHERE 
    TIMESTAMPDIFF(HOUR, session_end, next_session_start) <= 12
ORDER BY 
    user_id;
