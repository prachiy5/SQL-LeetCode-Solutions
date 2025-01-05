### Snaps Analysis

We are given two tables:

#### Table: Activities
| Column Name   | Type    |
|---------------|---------|
| activity_id   | int     |
| user_id       | int     |
| activity_type | enum    |
| time_spent    | decimal |

- `activity_id` is a unique identifier for each activity.
- `activity_type` can be either `'send'` or `'open'`.
- Each row contains:
  - `activity_id`: Unique ID for the activity.
  - `user_id`: The user who performed the activity.
  - `activity_type`: The type of activity (`send` or `open`).
  - `time_spent`: The time spent on this activity.

#### Table: Age
| Column Name | Type |
|-------------|------|
| user_id     | int  |
| age_bucket  | enum |

- `user_id` is a unique identifier for each user.
- `age_bucket` is the age group of the user and can be `'21-25'`, `'26-30'`, or `'31-35'`.

#### Task
Write a query to calculate the percentage of the total time spent on sending and opening snaps for each age group. Round the percentage to 2 decimal places.

---

### Example Input/Output

##### Input:
**Activities table:**
| activity_id | user_id | activity_type | time_spent |
|-------------|---------|---------------|------------|
| 7274        | 123     | open          | 4.50       |
| 2425        | 123     | send          | 3.50       |
| 1413        | 456     | send          | 5.67       |
| 2536        | 456     | open          | 3.00       |
| 8564        | 456     | send          | 8.24       |
| 5235        | 789     | send          | 6.24       |
| 4251        | 123     | open          | 1.25       |
| 1435        | 789     | open          | 5.25       |

**Age table:**
| user_id | age_bucket |
|---------|------------|
| 123     | 31-35      |
| 789     | 21-25      |
| 456     | 26-30      |

##### Output:
| age_bucket | send_perc | open_perc |
|------------|-----------|-----------|
| 31-35      | 37.84     | 62.16     |
| 26-30      | 82.26     | 17.74     |
| 21-25      | 54.31     | 45.69     |

##### Explanation:
1. For age group `31-35` (user ID 123):
   - Total `send` time: 3.50
   - Total `open` time: 4.50 + 1.25 = 5.75
   - Total time: 3.50 + 5.75 = 9.25
   - `send_perc = (3.50 / 9.25) * 100 = 37.84`
   - `open_perc = (5.75 / 9.25) * 100 = 62.16`

2. For age group `26-30` (user ID 456):
   - Total `send` time: 5.67 + 8.24 = 13.91
   - Total `open` time: 3.00
   - Total time: 13.91 + 3.00 = 16.91
   - `send_perc = (13.91 / 16.91) * 100 = 82.26`
   - `open_perc = (3.00 / 16.91) * 100 = 17.74`

3. For age group `21-25` (user ID 789):
   - Total `send` time: 6.24
   - Total `open` time: 5.25
   - Total time: 6.24 + 5.25 = 11.49
   - `send_perc = (6.24 / 11.49) * 100 = 54.31`
   - `open_perc = (5.25 / 11.49) * 100 = 45.69`

---

### Thought Process
1. **Join Tables:**
   - Join `Activities` and `Age` tables to associate `age_bucket` with each activity.
2. **Calculate Total Time:**
   - Compute the total time spent on all activities for each age group.
3. **Separate Activity Types:**
   - Calculate the total time for `send` and `open` activities separately for each age group.
4. **Calculate Percentages:**
   - Divide the total time for each activity type by the total time and multiply by 100 to get the percentage.
5. **Round Values:**
   - Use `ROUND()` to round percentages to 2 decimal places.

---

### Query
```sql
WITH cte AS (
    SELECT 
        ac.activity_id, 
        a.user_id, 
        ac.activity_type, 
        ac.time_spent, 
        a.age_bucket 
    FROM activities ac
    INNER JOIN age a ON ac.user_id = a.user_id
),

total_time AS (
    SELECT 
        age_bucket, 
        SUM(time_spent) AS total_time
    FROM cte
    GROUP BY age_bucket
),

open_time AS (
    SELECT 
        age_bucket, 
        SUM(time_spent) AS open_time
    FROM cte 
    WHERE activity_type = 'open'
    GROUP BY age_bucket
),

send_time AS (
    SELECT 
        age_bucket, 
        SUM(time_spent) AS send_time
    FROM cte 
    WHERE activity_type = 'send'
    GROUP BY age_bucket
),

open_perc AS (
    SELECT 
        tt.age_bucket, 
        ROUND(COALESCE(open_time, 0) * 100 / tt.total_time, 2) AS open_perc  
    FROM total_time tt
    LEFT JOIN open_time ot ON tt.age_bucket = ot.age_bucket  
),

send_perc AS (
    SELECT 
        tt.age_bucket, 
        ROUND(COALESCE(send_time, 0) * 100 / tt.total_time, 2) AS send_perc  
    FROM total_time tt
    LEFT JOIN send_time st ON tt.age_bucket = st.age_bucket  
)

SELECT 
    sp.age_bucket, 
    sp.send_perc, 
    op.open_perc
FROM send_perc sp
INNER JOIN open_perc op ON sp.age_bucket = op.age_bucket  
ORDER BY sp.age_bucket;
```

---

### Key Takeaways
- **Join Operations:**
  - Used `INNER JOIN` to link `Activities` and `Age` tables.
- **Activity Type Aggregation:**
  - Aggregated `send` and `open` times separately.
- **Percentage Calculation:**
  - Divided each activity type time by the total time for the age group.
- **Rounded Results:**
  - Used `ROUND()` to ensure percentages are formatted correctly.
- **Ordering Results:**
  - Ordered the final output by `age_bucket`.

---

### Related Topics
- SQL Joins
- Aggregation and Grouping
- Common Table Expressions (CTEs)
- Calculating Percentages in SQL
