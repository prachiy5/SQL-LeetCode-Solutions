### Employees With Deductions

We are given two tables:

#### Table: Employees
| Column Name  | Type |
|--------------|------|
| employee_id  | int  |
| needed_hours | int  |

- `employee_id` contains unique values for each employee.
- Each row represents an employee's ID and the minimum number of hours they need to work in a month to earn their salary.

#### Table: Logs
| Column Name | Type     |
|-------------|----------|
| employee_id | int      |
| in_time     | datetime |
| out_time    | datetime |

- `(employee_id, in_time, out_time)` is the primary key.
- Each row represents a work session, with `in_time` being the start time and `out_time` the end time.
- Sessions may span midnight (e.g., `out_time` is the next day).

#### Task
Write a query to find the IDs of employees who did not work the required number of hours (`needed_hours`).

- The number of hours worked is calculated by summing up all session durations for an employee.
- Each session duration is calculated in minutes, rounded up to the nearest minute.
- If an employee has no logs, their total work time is considered `0`.

The result should return the IDs of such employees in any order.

---

### Example Input/Output

##### Input:
**Employees table:**
| employee_id | needed_hours |
|-------------|--------------|
| 1           | 20           |
| 2           | 12           |
| 3           | 2            |

**Logs table:**
| employee_id | in_time             | out_time            |
|-------------|---------------------|---------------------|
| 1           | 2022-10-01 09:00:00 | 2022-10-01 17:00:00 |
| 1           | 2022-10-06 09:05:04 | 2022-10-06 17:09:03 |
| 1           | 2022-10-12 23:00:00 | 2022-10-13 03:00:01 |
| 2           | 2022-10-29 12:00:00 | 2022-10-29 23:58:58 |

##### Output:
| employee_id |
|-------------|
| 2           |
| 3           |

##### Explanation:
- **Employee 1:**
  - Session 1: 8 hours (480 minutes).
  - Session 2: 8 hours and 4 minutes (484 minutes).
  - Session 3: 4 hours and 1 minute (241 minutes).
  - Total: `1205` minutes = `20 hours 5 minutes`. They meet the required hours.

- **Employee 2:**
  - Session 1: 11 hours and 59 minutes (719 minutes).
  - Total: `719` minutes = `11 hours 59 minutes`. They fall short of the required hours.

- **Employee 3:**
  - No sessions logged. Total: `0` minutes. They fall short of the required hours.

---

### Thought Process
1. **Calculate Work Time for Each Employee:**
   - Use the `TIMESTAMPDIFF()` function to calculate session durations in seconds.
   - Divide the duration by `60` to get minutes and use `CEIL()` to round up.
   - Sum all session durations for each employee.
2. **Handle Employees Without Logs:**
   - Use a `RIGHT JOIN` to ensure all employees are included, even if they have no logs.
   - Use `IFNULL()` to handle null values by treating them as `0`.
3. **Compare Work Time with Needed Hours:**
   - Convert `needed_hours` into minutes for comparison.
   - Filter employees whose total work time is less than the required time.

---

### Query
```sql
WITH cte AS (
    SELECT e.employee_id, e.needed_hours,
           IFNULL(SUM(CEIL(TIMESTAMPDIFF(SECOND, l.in_time, l.out_time) / 60)), 0) AS number_of_hours_worked
    FROM logs l
    RIGHT JOIN employees e
    ON e.employee_id = l.employee_id
    GROUP BY e.employee_id
)

SELECT employee_id 
FROM cte
WHERE number_of_hours_worked < needed_hours * 60;
```

---

### Key Takeaways
- **Handling Nulls:** Use `RIGHT JOIN` and `IFNULL()` to include employees without logs.
- **Rounding Up:** Use `CEIL()` to ensure session durations are rounded up to the nearest minute.
- **Unit Conversion:** Compare total work time (in minutes) with `needed_hours` (converted to minutes).
- **Filtering:** Use a `WITH` clause to simplify logic by separating computation and filtering steps.

---

### Related Topics
- SQL Joins
- Window Functions and Aggregations
- Time Calculations in SQL
- Null Handling in SQL
