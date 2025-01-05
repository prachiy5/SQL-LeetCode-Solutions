# Find Overlapping Shifts

You are given a table `EmployeeShifts` with the following structure:

| Column Name  | Type |
|--------------|------|
| employee_id  | int  |
| start_time   | time |
| end_time     | time |

- `(employee_id, start_time)` is the unique key for this table.
- This table contains information about the shifts worked by employees, including the start and end times on a specific date.

### Task
Count the number of overlapping shifts for each employee. Two shifts are considered overlapping if one shift’s `end_time` is later than another shift’s `start_time`.

### Output Requirements
1. Return the `employee_id` and the count of overlapping shifts (`overlapping_shifts`) for each employee who has at least one overlapping shift.
2. Order the results by `employee_id` in ascending order.

### Input Example
#### EmployeeShifts Table:

| employee_id | start_time | end_time   |
|-------------|------------|------------|
| 1           | 08:00:00   | 12:00:00   |
| 1           | 11:00:00   | 15:00:00   |
| 1           | 14:00:00   | 18:00:00   |
| 2           | 09:00:00   | 17:00:00   |
| 2           | 16:00:00   | 20:00:00   |
| 3           | 10:00:00   | 12:00:00   |
| 3           | 13:00:00   | 15:00:00   |
| 3           | 16:00:00   | 18:00:00   |
| 4           | 08:00:00   | 10:00:00   |
| 4           | 09:00:00   | 11:00:00   |

### Output Example

| employee_id | overlapping_shifts |
|-------------|--------------------|
| 1           | 2                  |
| 2           | 1                  |
| 4           | 1                  |

### Explanation

1. **Employee 1**:
   - Shifts: `08:00:00-12:00:00`, `11:00:00-15:00:00`, `14:00:00-18:00:00`.
   - Overlapping pairs:
     - Shift 1 overlaps with Shift 2.
     - Shift 2 overlaps with Shift 3.
   - Total overlapping shifts: `2`.

2. **Employee 2**:
   - Shifts: `09:00:00-17:00:00`, `16:00:00-20:00:00`.
   - Overlapping pairs:
     - Shift 1 overlaps with Shift 2.
   - Total overlapping shifts: `1`.

3. **Employee 3**:
   - Shifts: `10:00:00-12:00:00`, `13:00:00-15:00:00`, `16:00:00-18:00:00`.
   - No overlaps.
   - Not included in the output.

4. **Employee 4**:
   - Shifts: `08:00:00-10:00:00`, `09:00:00-11:00:00`.
   - Overlapping pairs:
     - Shift 1 overlaps with Shift 2.
   - Total overlapping shifts: `1`.

### Thought Process
1. **Join the Table**:
   - Perform a self-join on `EmployeeShifts` to compare all pairs of shifts for the same employee.
2. **Identify Overlaps**:
   - Check if one shift’s `start_time` is earlier than another shift’s `start_time`.
   - Ensure that the `end_time` of the earlier shift is later than the `start_time` of the later shift.
3. **Count Overlaps**:
   - Use `COUNT(*)` to count the number of overlapping pairs for each `employee_id`.
4. **Filter and Order**:
   - Exclude employees with no overlapping shifts.
   - Order the result by `employee_id` in ascending order.

### Query
```sql
SELECT e1.employee_id,
       COUNT(*) AS overlapping_shifts
FROM EmployeeShifts e1
JOIN EmployeeShifts e2
  ON e1.employee_id = e2.employee_id
WHERE e1.start_time < e2.start_time
  AND e1.end_time > e2.start_time
GROUP BY e1.employee_id
ORDER BY e1.employee_id;
