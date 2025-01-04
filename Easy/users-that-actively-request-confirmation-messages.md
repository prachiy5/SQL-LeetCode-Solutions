# Users That Actively Request Confirmation Messages

### Problem Description
You are given two tables:
1. **Signups**: Contains user IDs and the timestamps of their signups.
2. **Confirmations**: Contains user IDs, timestamps when confirmation messages were requested, and the action taken (`confirmed` or `timeout`).

The goal is to find the IDs of users who requested confirmation messages at least twice within a 24-hour window. The action (`confirmed` or `timeout`) does not matter, only the request timestamps are relevant.

### Input Schema
**Table: Signups**
| Column Name | Type     | Description                     |
|-------------|----------|---------------------------------|
| user_id     | int      | Unique identifier of the user.  |
| time_stamp  | datetime | Signup time of the user.        |

**Table: Confirmations**
| Column Name | Type     | Description                               |
|-------------|----------|-------------------------------------------|
| user_id     | int      | Unique identifier of the user.            |
| time_stamp  | datetime | Timestamp when the confirmation was sent. |
| action      | ENUM     | Type of action (`confirmed`, `timeout`).  |

### Output Schema
| Column Name | Type | Description                            |
|-------------|------|----------------------------------------|
| user_id     | int  | ID of the user with multiple requests. |

### Example
#### Input
**Signups Table**
| user_id | time_stamp          |
|---------|---------------------|
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |

**Confirmations Table**
| user_id | time_stamp          | action    |
|---------|---------------------|-----------|
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-01-06 03:37:45 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 11:57:30 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |
| 2       | 2021-01-23 00:00:00 | timeout   |
| 6       | 2021-10-23 14:14:14 | confirmed |
| 6       | 2021-10-24 14:14:13 | timeout   |

#### Output
| user_id |
|---------|
| 2       |
| 3       |
| 6       |

#### Explanation
- User 2 requested messages exactly 24 hours apart.
- User 3 requested messages within 6 minutes and 59 seconds of each other.
- User 6 requested messages within 23 hours, 59 minutes, and 59 seconds of each other.
- User 7 requested messages 24 hours and 1 second apart, so they are excluded.

---

### Thought Process
1. **Identify Pairwise Time Differences**: Use the `LEAD()` function to calculate the difference in seconds between consecutive `time_stamp` values for each `user_id`.
2. **Filter by Time Window**: Retain only the rows where the time difference is less than or equal to 24 hours (86400 seconds).
3. **Find Unique User IDs**: Select distinct `user_id` values from the filtered data.

---

### SQL Solution
```sql
WITH cte AS (
    SELECT 
        user_id,
        TIMESTAMPDIFF(SECOND, time_stamp, LEAD(time_stamp, 1) OVER (
            PARTITION BY user_id ORDER BY time_stamp
        )) AS time_diff
    FROM 
        Confirmations
)
SELECT DISTINCT 
    user_id
FROM 
    cte
WHERE 
    time_diff <= 86400;
```

---

### Solution Explanation
1. **CTE to Calculate Time Differences**:
   - Use the `LEAD()` function to get the next `time_stamp` for each `user_id`.
   - Compute the time difference (in seconds) between the current and next `time_stamp` using `TIMESTAMPDIFF(SECOND, ...)`.
   - Partition by `user_id` to ensure comparisons are done only within the same user.

2. **Filter by Time Window**:
   - Retain rows where the `time_diff` is less than or equal to 86400 seconds (24 hours).

3. **Select Unique User IDs**:
   - Use `DISTINCT` to ensure each user ID appears only once in the output.

---

### Key Takeaways
1. **Window Functions**: `LEAD()` is useful for comparing consecutive rows within a partition.
2. **Time Calculations**: Use `TIMESTAMPDIFF()` to calculate differences in specific time units.
3. **Filtering Results**: Combine filtering and aggregation to narrow down relevant data.

---

### Related Topics
- Window Functions
- Date and Time Functions
- Common Table Expressions (CTEs)
