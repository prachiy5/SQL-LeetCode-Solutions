#User Activity for the Past 30 Days I

### Problem

#### Table Overview

**Activity**

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |

- The `activity_type` column is an ENUM with possible values: `'open_session', 'end_session', 'scroll_down', 'send_message'`.
- Each session belongs to exactly one user.
- The table may have duplicate rows.
- Each row records a user activity on a given date for a specific session.

#### Task
Find the **daily active user count** for the last 30 days ending on `2019-07-27` inclusively. A user is considered active on a day if they performed at least one activity on that day.

---

### Example

**Input:**

`Activity` table:

| user_id | session_id | activity_date | activity_type |
|---------|------------|---------------|---------------|
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |

**Output:**

| day        | active_users |
|------------|--------------|
| 2019-07-20 | 2            |
| 2019-07-21 | 2            |

**Explanation:**
- On `2019-07-20`, two users (`user_id 1` and `user_id 2`) were active.
- On `2019-07-21`, two users (`user_id 2` and `user_id 3`) were active.
- Days without active users are not included in the result.

---

### Thought Process

1. **Filter the Date Range:**
   - Use `WHERE` to limit activities to those that occurred in the last 30 days ending on `2019-07-27`.
   - Calculate the date range dynamically using `DATE` and `INTERVAL` functions.

2. **Count Unique Users Per Day:**
   - Use `COUNT(DISTINCT user_id)` to count unique users active on each day.

3. **Group by Day:**
   - Use `GROUP BY activity_date` to calculate the count for each day separately.

4. **Return the Results:**
   - Select `activity_date` as `day` and the calculated user count as `active_users`.
   - Days without active users are excluded automatically by the grouping logic.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
    activity_date AS day, 
    COUNT(DISTINCT user_id) AS active_users 
FROM activity
WHERE 
    activity_date BETWEEN DATE('2019-07-27') - INTERVAL 29 DAY AND DATE('2019-07-27')
GROUP BY activity_date;
```

---

### Solution Explanation

1. **Filter Rows:**
   - The `WHERE` clause ensures that only rows with `activity_date` in the range `2019-06-28` to `2019-07-27` are included.

2. **Count Unique Users:**
   - The `COUNT(DISTINCT user_id)` aggregates the number of unique users active on each day.

3. **Group by Date:**
   - The `GROUP BY activity_date` groups activities by day, enabling the calculation of active users per day.

4. **Output Columns:**
   - The query outputs `activity_date` as `day` and the active user count as `active_users`.

---

### Key Takeaways

- **Date Filtering:** Use `BETWEEN` to specify a range of dates.
- **Counting Distinct Values:** Use `COUNT(DISTINCT column)` to count unique occurrences.
- **Grouping by Columns:** Use `GROUP BY` to aggregate data at a granular level.

---

### Related Topics
- SQL Aggregate Functions
- Filtering with WHERE Clause
- Date and Time Functions in SQL
