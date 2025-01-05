### Server Utilization Time
We are tasked with finding the total time, in full days, during which servers were running. Each server has a `start` and `stop` session status recorded in the `Servers` table. The total running time should be rounded down to the nearest number of full days.

#### Table: Servers
| Column Name    | Type     |
|----------------|----------|
| server_id      | int      |
| status_time    | datetime |
| session_status | enum     |

- **Primary Key:** (`server_id`, `status_time`, `session_status`)
- **session_status:** Can be either 'start' or 'stop'.

Each row records the start or stop time of a session for a specific server.

### Output Requirements

The output table should contain:
- `total_uptime_days`: Total time in full days across all servers, rounded down.

### Example

#### Input
**Servers table:**
| server_id | status_time         | session_status |
|-----------|---------------------|----------------|
| 3         | 2023-11-04 16:29:47 | start          |
| 3         | 2023-11-05 01:49:47 | stop           |
| 3         | 2023-11-25 01:37:08 | start          |
| 3         | 2023-11-25 03:50:08 | stop           |
| 1         | 2023-11-13 03:05:31 | start          |
| 1         | 2023-11-13 11:10:31 | stop           |
| 4         | 2023-11-29 15:11:17 | start          |
| 4         | 2023-11-29 15:42:17 | stop           |
| 4         | 2023-11-20 00:31:44 | start          |
| 4         | 2023-11-20 07:03:44 | stop           |
| 1         | 2023-11-20 00:27:11 | start          |
| 1         | 2023-11-20 01:41:11 | stop           |
| 3         | 2023-11-04 23:16:48 | start          |
| 3         | 2023-11-05 01:15:48 | stop           |
| 4         | 2023-11-30 15:09:18 | start          |
| 4         | 2023-11-30 20:48:18 | stop           |
| 4         | 2023-11-25 21:09:06 | start          |
| 4         | 2023-11-26 04:58:06 | stop           |
| 5         | 2023-11-16 19:42:22 | start          |
| 5         | 2023-11-16 21:08:22 | stop           |

#### Output
| total_uptime_days |
|-------------------|
| 1                 |

### Thought Process
1. **Identify Session Durations:**
   - Use the `LEAD()` window function to pair each `start` session with the corresponding `stop` session.
   - Ensure the `stop_time` is not null for valid pairs.

2. **Calculate Session Durations:**
   - Compute the duration between `start_time` and `stop_time` in seconds, then convert to hours.

3. **Aggregate Total Uptime:**
   - Sum up the durations for all servers and convert the total hours to days.
   - Use `FLOOR()` to round down to the nearest whole number of days.

4. **Output the Total Days:**
   - Return the total uptime in full days as a single value.

### Query
```sql
WITH cte AS (
    SELECT 
        server_id, 
        status_time AS start_time,
        session_status,
        LEAD(status_time) OVER(PARTITION BY server_id ORDER BY status_time) AS stop_time
    FROM servers
),

cte2 AS (
    SELECT 
        server_id,
        start_time,
        stop_time,
        ROUND(TIMESTAMPDIFF(SECOND, start_time, stop_time) / 3600, 2) AS session_hours
    FROM cte
    WHERE session_status = 'start'
      AND stop_time IS NOT NULL
    ORDER BY server_id, start_time
)

SELECT  
    FLOOR(SUM(session_hours) / 24) AS total_uptime_days
FROM cte2;
