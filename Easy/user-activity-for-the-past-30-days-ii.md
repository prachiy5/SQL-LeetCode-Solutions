# User Activity for the Past 30 Days-II

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
Find the **average number of sessions per user** for the last 30 days ending on `2019-07-27` inclusively, rounded to 2 decimal places. Count only sessions with at least one activity during this time period.

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
| 3       | 5          | 2019-07-21    | open_session  |
| 3       | 5          | 2019-07-21    | scroll_down   |
| 3       | 5          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |

**Output:**

| average_sessions_per_user |
|---------------------------|
| 1.33                      |

**Explanation:**
- User 1 and 2 each had 1 session in the past 30 days while user 3 had 2 sessions. 
- The average is calculated as `(1 + 1 + 2) / 3 = 1.33`.

---

### Thought Process

1. **Filter the Date Range:**
   - Use `WHERE` to limit activities to those that occurred in the last 30 days ending on `2019-07-27`.
   - Calculate the date range dynamically using `DATE_SUB()`.

2. **Count Unique Sessions Per User:**
   - Use `COUNT(DISTINCT session_id)` to calculate the number of unique sessions for each user.

3. **Calculate the Average:**
   - Use a CTE (Common Table Expression) to store the session counts for each user.
   - Use `SUM(sessions)` divided by `COUNT(DISTINCT user_id)` to calculate the average.
   - Round the result to 2 decimal places using `ROUND()`.

4. **Handle Edge Cases:**
   - Use `IFNULL()` to return `0` if there are no users.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT 
    user_id, 
    COUNT(DISTINCT session_id) AS sessions
  FROM activity
  WHERE 
    activity_date BETWEEN DATE_SUB('2019-07-27', INTERVAL 29 DAY) AND '2019-07-27'
  GROUP BY user_id
)

SELECT 
  IFNULL(ROUND(SUM(sessions) / COUNT(DISTINCT user_id), 2), 0) AS average_sessions_per_user 
FROM cte;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Calculates the number of unique sessions for each user active during the specified time period.

2. **Calculate the Average:**
   - Sums up the session counts and divides by the total number of users.
   - Rounds the result to 2 decimal places for readability.
   - Ensures that `0` is returned if there are no users using `IFNULL()`.

3. **Output Columns:**
   - The query outputs a single column, `average_sessions_per_user`.

---

### Key Takeaways

- **Date Filtering:** Use `DATE_SUB()` to dynamically calculate date ranges.
- **Aggregating Data:** Use `COUNT(DISTINCT column)` to count unique values.
- **Handling Null Values:** Use `IFNULL()` to handle cases where no data is available.

---

### Related Topics
- SQL Aggregate Functions
- Filtering with WHERE Clause
- Common Table Expressions (CTEs)
- Rounding in SQL
