### Question:

Table: EmployeeShifts

| Column Name | Type     |
|-------------|----------|
| employee_id | int      |
| start_time  | datetime |
| end_time    | datetime |

- `(employee_id, start_time)` is the unique key for this table.
- This table contains information about the shifts worked by employees, including the start time and end time.

Write a solution to analyze overlapping shifts for each employee. Two shifts are considered overlapping if they occur on the same date and one shift's `end_time` is later than another shift's `start_time`.

For each employee, calculate the following:

1. The maximum number of shifts that overlap at any given time.
2. The total duration of all overlaps in minutes.

Return the result table ordered by `employee_id` in ascending order.

---

### Example:

#### Input:

`EmployeeShifts` table:

| employee_id | start_time          | end_time            |
|-------------|---------------------|---------------------|
| 1           | 2023-10-01 09:00:00 | 2023-10-01 17:00:00 |
| 1           | 2023-10-01 15:00:00 | 2023-10-01 23:00:00 |
| 1           | 2023-10-01 16:00:00 | 2023-10-02 00:00:00 |
| 2           | 2023-10-01 09:00:00 | 2023-10-01 17:00:00 |
| 2           | 2023-10-01 11:00:00 | 2023-10-01 19:00:00 |
| 3           | 2023-10-01 09:00:00 | 2023-10-01 17:00:00 |

#### Output:

| employee_id | max_overlapping_shifts | total_overlap_duration |
|-------------|-------------------------|-------------------------|
| 1           | 3                       | 600                     |
| 2           | 2                       | 360                     |
| 3           | 1                       | 0                       |

#### Explanation:

**Employee 1:**
- Shifts:
  1. 2023-10-01 09:00:00 to 2023-10-01 17:00:00
  2. 2023-10-01 15:00:00 to 2023-10-01 23:00:00
  3. 2023-10-01 16:00:00 to 2023-10-02 00:00:00
- Maximum overlapping shifts: 3 (from 16:00 to 17:00).
- Total overlap duration:
  - 2 hours (15:00 to 17:00) between the 1st and 2nd shifts.
  - 1 hour (16:00 to 17:00) between the 1st and 3rd shifts.
  - 7 hours (16:00 to 23:00) between the 2nd and 3rd shifts.
  - Total = 10 hours = 600 minutes.

**Employee 2:**
- Shifts:
  1. 2023-10-01 09:00:00 to 2023-10-01 17:00:00
  2. 2023-10-01 11:00:00 to 2023-10-01 19:00:00
- Maximum overlapping shifts: 2.
- Total overlap duration:
  - 6 hours (11:00 to 17:00) between the 1st and 2nd shifts.
  - Total = 360 minutes.

**Employee 3:**
- Only 1 shift, so there are no overlaps.
- Maximum overlapping shifts: 1.
- Total overlap duration: 0 minutes.

---

### Thought Process:

1. **Identify Overlaps:**
   - Use a self-join to compare shifts for the same employee.
   - Identify overlaps where the `start_time` of one shift is between the `start_time` and `end_time` of another shift.

2. **Calculate Total Overlap Duration:**
   - For each overlapping pair, calculate the overlap duration in minutes.
   - Sum the overlap durations for each employee.

3. **Determine Maximum Overlaps:**
   - For each employee, count the maximum number of overlapping shifts at any given time.

4. **Handle Employees Without Overlaps:**
   - Ensure employees without overlaps are included with `max_overlapping_shifts = 1` and `total_overlap_duration = 0`.

5. **Return Results:**
   - Return `employee_id`, `max_overlapping_shifts`, and `total_overlap_duration`, sorted by `employee_id`.

---

### Query:

```sql
WITH OverlappingShifts AS (
    SELECT 
        s1.employee_id,
        s1.start_time,
        s1.end_time,
        s2.start_time AS overlap_start,
        s2.end_time AS overlap_end
    FROM EmployeeShifts s1
    JOIN EmployeeShifts s2 
        ON s1.employee_id = s2.employee_id
        AND s2.start_time > s1.start_time
        AND s2.start_time < s1.end_time
),
ShiftOverlapSummary AS (
    SELECT 
        employee_id,
        COUNT(*) AS overlap_count,
        MIN(overlap_start) AS earliest_overlap_start,
        MAX(overlap_end) AS latest_overlap_end
    FROM OverlappingShifts
    GROUP BY employee_id, start_time, end_time
),
TotalOverlapDuration AS (
    SELECT 
        employee_id,
        SUM(TIMESTAMPDIFF(MINUTE, overlap_start, LEAST(overlap_end, end_time))) AS total_overlap_minutes
    FROM OverlappingShifts
    GROUP BY employee_id
),
MaxOverlappingShifts AS (
    SELECT 
        employee_id,
        MAX(overlap_count) AS max_overlapping_shifts
    FROM ShiftOverlapSummary
    GROUP BY employee_id
)
SELECT 
    tod.employee_id,
    COALESCE(mos.max_overlapping_shifts + 1, 1) AS max_overlapping_shifts,
    COALESCE(tod.total_overlap_minutes, 0) AS total_overlap_duration
FROM TotalOverlapDuration tod
JOIN MaxOverlappingShifts mos ON tod.employee_id = mos.employee_id
UNION
SELECT DISTINCT 
    es.employee_id, 
    1 AS max_overlapping_shifts, 
    0 AS total_overlap_duration
FROM EmployeeShifts es
WHERE NOT EXISTS (
    SELECT 1 
    FROM OverlappingShifts os
    WHERE os.employee_id = es.employee_id
)
ORDER BY employee_id;
