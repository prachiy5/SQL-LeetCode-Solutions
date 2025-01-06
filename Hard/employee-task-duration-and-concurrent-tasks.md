### Employee Task Duration and Concurrent Tasks

Table: Tasks

| Column Name   | Type     |
|---------------|----------|
| task_id       | int      |
| employee_id   | int      |
| start_time    | datetime |
| end_time      | datetime |

- `(task_id, employee_id)` is the primary key for this table.
- Each row in this table contains the task identifier, the employee identifier, and the start and end times of each task.

Write a solution to find the total duration of tasks for each employee and the maximum number of concurrent tasks an employee handled at any point in time. The total duration should be rounded down to the nearest number of full hours.

Return the result table ordered by `employee_id` in ascending order.

---

### Example:

#### Input:

Tasks table:

| task_id | employee_id | start_time          | end_time            |
|---------|-------------|---------------------|---------------------|
| 1       | 1001        | 2023-05-01 08:00:00 | 2023-05-01 09:00:00 |
| 2       | 1001        | 2023-05-01 08:30:00 | 2023-05-01 10:30:00 |
| 3       | 1001        | 2023-05-01 11:00:00 | 2023-05-01 12:00:00 |
| 7       | 1001        | 2023-05-01 13:00:00 | 2023-05-01 15:30:00 |
| 4       | 1002        | 2023-05-01 09:00:00 | 2023-05-01 10:00:00 |
| 5       | 1002        | 2023-05-01 09:30:00 | 2023-05-01 11:30:00 |
| 6       | 1003        | 2023-05-01 14:00:00 | 2023-05-01 16:00:00 |

#### Output:

| employee_id | total_task_hours | max_concurrent_tasks |
|-------------|------------------|----------------------|
| 1001        | 6                | 2                    |
| 1002        | 2                | 2                    |
| 1003        | 2                | 1                    |

#### Explanation:

1. **For Employee ID 1001**:
   - Task 1 and Task 2 overlap from 08:30 to 09:00 (30 minutes).
   - Task 7 has a duration of 150 minutes (2 hours and 30 minutes).
   - Total task time: 60 (Task 1) + 120 (Task 2) + 60 (Task 3) + 150 (Task 7) - 30 (overlap) = 360 minutes = 6 hours.
   - Maximum concurrent tasks: 2 (during the overlap period).

2. **For Employee ID 1002**:
   - Task 4 and Task 5 overlap from 09:30 to 10:00 (30 minutes).
   - Total task time: 60 (Task 4) + 120 (Task 5) - 30 (overlap) = 150 minutes = 2 hours and 30 minutes.
   - Total task hours (rounded down): 2 hours.
   - Maximum concurrent tasks: 2 (during the overlap period).

3. **For Employee ID 1003**:
   - No overlapping tasks.
   - Total task time: 120 minutes = 2 hours.
   - Maximum concurrent tasks: 1.

---

### Thought Process:
1. **Calculate Task Durations**:
   - Use `TIMESTAMPDIFF(MINUTE, start_time, end_time)` to compute the duration of each task.
2. **Identify Overlaps**:
   - Use a self-join to compare tasks of the same employee and check for overlaps (`start_time` and `end_time` conditions).
3. **Concurrent Tasks**:
   - Count the number of overlapping tasks for each time interval to determine the maximum concurrent tasks.
4. **Aggregate Results**:
   - Sum up the durations of tasks for each employee while avoiding double-counting overlapping durations.
   - Round down the total duration to the nearest hour.
5. **Return Results**:
   - Output the `employee_id`, `total_task_hours`, and `max_concurrent_tasks` sorted by `employee_id` in ascending order.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        t1.employee_id,
        SUM(
            CASE 
                WHEN t1.task_id = t2.task_id THEN 
                    TIMESTAMPDIFF(MINUTE, t1.start_time, t1.end_time) 
                ELSE 
                    -TIMESTAMPDIFF(MINUTE, t2.start_time, t1.end_time) 
            END
        ) AS task_hours,
        COUNT(t2.task_id) AS concurrent_hours
    FROM 
        tasks t1
    INNER JOIN 
        tasks t2
    ON 
        t1.employee_id = t2.employee_id
        AND t1.start_time <= t2.start_time
        AND t1.end_time > t2.start_time
    GROUP BY 
        t1.employee_id, t1.task_id
)

SELECT 
    employee_id,
    FLOOR(SUM(task_hours) / 60) AS total_task_hours,
    MAX(concurrent_hours) AS max_concurrent_tasks
FROM 
    cte
GROUP BY 
    employee_id
ORDER BY 
    employee_id;
