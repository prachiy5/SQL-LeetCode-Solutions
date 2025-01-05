### New Users Daily Count
We are given a table:

#### Table: `Traffic`

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| activity      | enum    |
| activity_date | date    |

- The table may have duplicate rows.
- The `activity` column is of ENUM type, with possible values: `('login', 'logout', 'jobs', 'groups', 'homepage')`.

### Task
Write a SQL query to report, for every date within at most 90 days from `2019-06-30`, the number of users that logged in for the first time on that date. The result table should include only those dates with non-zero user counts. Return the result in any order.

### Example
#### Input:
**Traffic table:**

| user_id | activity | activity_date |
|---------|----------|---------------|
| 1       | login    | 2019-05-01    |
| 1       | homepage | 2019-05-01    |
| 1       | logout   | 2019-05-01    |
| 2       | login    | 2019-06-21    |
| 2       | logout   | 2019-06-21    |
| 3       | login    | 2019-01-01    |
| 3       | jobs     | 2019-01-01    |
| 3       | logout   | 2019-01-01    |
| 4       | login    | 2019-06-21    |
| 4       | groups   | 2019-06-21    |
| 4       | logout   | 2019-06-21    |
| 5       | login    | 2019-03-01    |
| 5       | logout   | 2019-03-01    |
| 5       | login    | 2019-06-21    |
| 5       | logout   | 2019-06-21    |

#### Output:

| login_date | user_count |
|------------|------------|
| 2019-05-01 | 1          |
| 2019-06-21 | 2          |

#### Explanation:
- User `1` first logged in on `2019-05-01`.
- Users `2` and `4` first logged in on `2019-06-21`.
- User `3` first logged in on `2019-01-01`, which is not within 90 days of `2019-06-30`.
- User `5` first logged in on `2019-03-01` and is not counted on `2019-06-21` as their first login date is earlier.

---

### Thought Process
1. **Identify First Login Date**: Use the `MIN()` function to find the first login date for each user where `activity = 'login'`.
2. **Filter by Date Range**: Only consider login dates within 90 days from `2019-06-30`. This range is `[2019-04-01, 2019-06-30]`.
3. **Count Users Per Date**: Group the data by the login date and count the number of users who logged in for the first time on each date.

---

### SQL Query
```sql
WITH first_login AS (
    SELECT 
        user_id, 
        MIN(activity_date) AS login_date
    FROM Traffic
    WHERE activity = 'login'
    GROUP BY user_id
)

SELECT 
    login_date, 
    COUNT(user_id) AS user_count
FROM first_login
WHERE login_date BETWEEN DATE_SUB('2019-06-30', INTERVAL 90 DAY) AND '2019-06-30'
GROUP BY login_date;
```

---

### Key Takeaways
1. Use a `WITH` clause (Common Table Expression) to simplify complex queries by breaking them into smaller, logical steps.
2. The `MIN()` function is useful for finding the earliest occurrence of an event (e.g., the first login date).
3. `DATE_SUB()` helps calculate date ranges relative to a given date.
4. Use `GROUP BY` to aggregate data by specific columns (e.g., `login_date`) for summary statistics.

### Related Topics
- Aggregation Functions
- Filtering with `WHERE`
- Date Functions
- Common Table Expressions (CTEs)
