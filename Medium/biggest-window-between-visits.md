# Biggest Window Between Visits
### Problem Statement

You are given a table `UserVisits` that contains logs of the dates that users visited a certain retailer. For each user, find out the largest window of days between consecutive visits, including the last visit and today's date ('2021-1-1'). Return the result table ordered by `user_id`.

### Input Tables
#### UserVisits
| Column Name | Type |
|-------------|------|
| user_id     | int  |
| visit_date  | date |

- `user_id`: The ID of the user.
- `visit_date`: The date the user visited the retailer.

The table might contain duplicate rows, and there is no primary key.

### Output Table
| Column Name      | Type |
|------------------|------|
| user_id          | int  |
| biggest_window   | int  |

- `user_id`: The ID of the user.
- `biggest_window`: The largest gap in days between consecutive visits (or between the last visit and '2021-1-1').

### Example Input
#### UserVisits
| user_id | visit_date  |
|---------|-------------|
| 1       | 2020-11-28  |
| 1       | 2020-10-20  |
| 1       | 2020-12-3   |
| 2       | 2020-10-5   |
| 2       | 2020-12-9   |
| 3       | 2020-11-11  |

### Example Output
| user_id | biggest_window |
|---------|----------------|
| 1       | 39             |
| 2       | 65             |
| 3       | 51             |

### Explanation
1. For user 1:
   - Gap between `2020-10-20` and `2020-11-28`: 39 days.
   - Gap between `2020-11-28` and `2020-12-3`: 5 days.
   - Gap between `2020-12-3` and `2021-1-1`: 29 days.
   - The largest gap is **39 days**.
2. For user 2:
   - Gap between `2020-10-5` and `2020-12-9`: 65 days.
   - Gap between `2020-12-9` and `2021-1-1`: 23 days.
   - The largest gap is **65 days**.
3. For user 3:
   - Only one gap: Between `2020-11-11` and `2021-1-1`: 51 days.

### Query
```sql
WITH cte AS (
    SELECT 
        user_id,
        visit_date,
        DATEDIFF(LEAD(visit_date, 1, '2021-1-1') OVER (PARTITION BY user_id ORDER BY visit_date), visit_date) AS gap
    FROM UserVisits
)

SELECT 
    user_id, 
    MAX(gap) AS biggest_window
FROM cte
GROUP BY user_id
ORDER BY user_id;
