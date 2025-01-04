# Find Total Time Spent by Each Employee

### Problem

#### Table: Employees

| Column Name | Type |
|-------------|------|
| emp_id      | int  |
| event_day   | date |
| in_time     | int  |
| out_time    | int  |

- `(emp_id, event_day, in_time)` is the primary key.
- `event_day` indicates the date of the event.
- `in_time` and `out_time` represent the entry and exit times (in minutes) of an employee.
- Employees may have multiple entries and exits on the same day.

#### Task

Calculate the total time (in minutes) spent by each employee on each day.
- For a single entry, the time spent is `out_time - in_time`.
- Sum the time spent for all entries on the same day for each employee.

---

### Example

#### Input

**Employees Table**:

| emp_id | event_day  | in_time | out_time |
|--------|------------|---------|----------|
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |

#### Output

| day        | emp_id | total_time |
|------------|--------|------------|
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |

#### Explanation

- Employee `1`:
  - On `2020-11-28`: Two events:
    - (32 - 4) = 28
    - (200 - 55) = 145
    - Total = 28 + 145 = 173
  - On `2020-12-03`: One event:
    - (42 - 1) = 41

- Employee `2`:
  - On `2020-11-28`: One event:
    - (33 - 3) = 30
  - On `2020-12-09`: One event:
    - (74 - 47) = 27

---

### Thought Process

1. **Calculate Time per Entry**:
   - Use `out_time - in_time` for each row.

2. **Group by Employee and Day**:
   - Use `GROUP BY` on `event_day` and `emp_id` to group the rows by date and employee.

3. **Aggregate Time**:
   - Sum up the time for all events within the same day for each employee.

4. **Return Results**:
   - Select `event_day` (renamed as `day`), `emp_id`, and the total time spent (`total_time`).
   - Order the results if necessary (optional in this task).

---

### Solution

```sql
SELECT 
    event_day AS day,
    emp_id,
    SUM(out_time - in_time) AS total_time
FROM 
    employees
GROUP BY 
    event_day, emp_id;
```

---

### Solution Explanation

1. **SELECT Clause**:
   - `event_day AS day`: Renames `event_day` to `day` for clarity.
   - `emp_id`: Specifies the employee.
   - `SUM(out_time - in_time)`: Calculates the total time spent by summing the difference of `out_time` and `in_time` for each entry.

2. **GROUP BY Clause**:
   - Groups data by `event_day` and `emp_id` to aggregate the time for each employee on each day.

---

### Key Takeaways

- Use `SUM()` to aggregate numeric columns.
- Use `GROUP BY` to group data by specific columns for aggregation.
- Rename columns in the output for better readability.

---

### Related Topics

- SQL Aggregation Functions
- Grouping Data in SQL
- Time Calculations in Databases
