# Average Time of Process per Machine

### Problem

#### Table: Activity

| Column Name    | Type  |
|----------------|-------|
| machine_id     | int   |
| process_id     | int   |
| activity_type  | enum  |
| timestamp      | float |

- `(machine_id, process_id, activity_type)` is the primary key.
- `activity_type` can be `start` or `end`.
- `timestamp` is the time in seconds when the activity occurred.
- Each `start` activity will always have a corresponding `end` activity for the same `(machine_id, process_id)` pair.

#### Task

Calculate the **average processing time** for each machine. The **processing time** for a process is the difference between the `end` timestamp and the `start` timestamp. The **average processing time** for a machine is the total time for all its processes divided by the number of processes. Round the result to 3 decimal places.

### Example

#### Input

**Activity** table:

| machine_id | process_id | activity_type | timestamp |
|------------|------------|---------------|-----------|
| 0          | 0          | start         | 0.712     |
| 0          | 0          | end           | 1.520     |
| 0          | 1          | start         | 3.140     |
| 0          | 1          | end           | 4.120     |
| 1          | 0          | start         | 0.550     |
| 1          | 0          | end           | 1.550     |
| 1          | 1          | start         | 0.430     |
| 1          | 1          | end           | 1.420     |
| 2          | 0          | start         | 4.100     |
| 2          | 0          | end           | 4.512     |
| 2          | 1          | start         | 2.500     |
| 2          | 1          | end           | 5.000     |

#### Output

| machine_id | processing_time |
|------------|-----------------|
| 0          | 0.894           |
| 1          | 0.995           |
| 2          | 1.456           |

#### Explanation

1. **Machine 0**:
   - Process 0: `1.520 - 0.712 = 0.808`
   - Process 1: `4.120 - 3.140 = 0.980`
   - Average: `(0.808 + 0.980) / 2 = 0.894`

2. **Machine 1**:
   - Process 0: `1.550 - 0.550 = 1.000`
   - Process 1: `1.420 - 0.430 = 0.990`
   - Average: `(1.000 + 0.990) / 2 = 0.995`

3. **Machine 2**:
   - Process 0: `4.512 - 4.100 = 0.412`
   - Process 1: `5.000 - 2.500 = 2.500`
   - Average: `(0.412 + 2.500) / 2 = 1.456`

---

### Thought Process

1. **Calculate Processing Time for Each Process**:
   - Pair `start` and `end` timestamps for the same `(machine_id, process_id)`.
   - Compute `processing_time` as `end_time - start_time`.

2. **Average Processing Time Per Machine**:
   - Group processes by `machine_id`.
   - Calculate the average of `processing_time` for each machine.

3. **Round Results**:
   - Round the `processing_time` to 3 decimal places.

---

### Solution

```sql
SELECT 
    machine_id,
    ROUND(AVG(processing_time), 3) AS processing_time
FROM (
    SELECT
        A1.machine_id,
        A1.process_id,
        A1.timestamp AS start_time,
        A2.timestamp AS end_time,
        (A2.timestamp - A1.timestamp) AS processing_time
    FROM
        Activity A1
    JOIN
        Activity A2 ON A1.machine_id = A2.machine_id
                   AND A1.process_id = A2.process_id
                   AND A1.activity_type = 'start'
                   AND A2.activity_type = 'end'
) AS ProcessTimes
GROUP BY machine_id;
```

---

### Solution Explanation

1. **Subquery:**
   - Match `start` and `end` activities for the same `(machine_id, process_id)` pair.
   - Compute the `processing_time` as `end_time - start_time`.

2. **Outer Query:**
   - Group by `machine_id` to calculate the average `processing_time` for each machine.
   - Use `ROUND(..., 3)` to format the result to 3 decimal places.

3. **Result Order:**
   - The result can be returned in any order as per the problem statement.

---

### Key Takeaways

- Use `JOIN` to pair related rows within a table based on conditions.
- Use aggregation (`AVG`) to calculate averages.
- Use `ROUND` for precise formatting of numerical results.

---

### Related Topics

- SQL Joins
- Aggregation and Grouping
- Mathematical Functions in SQL
