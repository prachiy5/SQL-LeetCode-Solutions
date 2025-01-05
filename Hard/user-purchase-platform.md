# User Purchase Platform

You are given a table:

### Spending Table
| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| spend_date  | date    |
| platform    | enum    |
| amount      | int     |

- `(user_id, spend_date, platform)` is the primary key of this table.
- The table logs the history of the spending of users who make purchases using the website's desktop or mobile platform.
- The `platform` column is an ENUM with possible values `('desktop', 'mobile')`.

### Task

For each `spend_date`, find the following:
1. The total number of users who used only the **mobile** platform.
2. The total number of users who used only the **desktop** platform.
3. The total number of users who used **both** platforms on the same date.

Additionally, calculate the total amount spent by users for each platform (mobile only, desktop only, both).

### Output
Return the result table with the following columns:
1. **spend_date**: The date of the spending.
2. **platform**: Indicates whether the spending was on `desktop`, `mobile`, or `both` platforms.
3. **total_amount**: The total amount spent for the respective platform on that date.
4. **total_users**: The total number of users for the respective platform on that date.

Return the result in any order.

---

### Example Input
#### Spending Table
| user_id | spend_date | platform | amount |
|---------|------------|----------|--------|
| 1       | 2019-07-01 | mobile   | 100    |
| 1       | 2019-07-01 | desktop  | 100    |
| 2       | 2019-07-01 | mobile   | 100    |
| 2       | 2019-07-02 | mobile   | 100    |
| 3       | 2019-07-01 | desktop  | 100    |
| 3       | 2019-07-02 | desktop  | 100    |

---

### Example Output
| spend_date | platform | total_amount | total_users |
|------------|----------|--------------|-------------|
| 2019-07-01 | desktop  | 100          | 1           |
| 2019-07-01 | mobile   | 100          | 1           |
| 2019-07-01 | both     | 200          | 1           |
| 2019-07-02 | desktop  | 100          | 1           |
| 2019-07-02 | mobile   | 100          | 1           |
| 2019-07-02 | both     | 0            | 0           |

---

### Thought Process

1. **Group Spending by User and Date**:
   - Aggregate the `amount` spent by each user on both `mobile` and `desktop` platforms for each `spend_date`.
   - This helps identify the total spending by platform for each user and date.

2. **Classify Users by Platform Usage**:
   - Determine whether a user used only `mobile`, only `desktop`, or `both` platforms on a given date based on their spending activity.

3. **Create All Possible Platform Categories**:
   - Ensure every `spend_date` includes all three platform categories (`desktop`, `mobile`, `both`), even if no users fall into a particular category on that date.

4. **Aggregate Data for Each Platform**:
   - Sum the total spending and count the number of users for each platform category (`desktop`, `mobile`, `both`) on each `spend_date`.

5. **Join and Finalize Output**:
   - Combine the aggregated data with the possible platform categories and compute the results.

---

### Query
```sql
WITH cte AS (
    SELECT 
        spend_date,
        user_id,
        SUM(CASE WHEN platform = 'mobile' THEN amount ELSE 0 END) AS mobile_amount,
        SUM(CASE WHEN platform = 'desktop' THEN amount ELSE 0 END) AS desktop_amount
    FROM 
        spending
    GROUP BY 
        spend_date, user_id
),

cte2 AS (
    SELECT 
        spend_date,
        user_id,
        CASE 
            WHEN mobile_amount > 0 AND desktop_amount > 0 THEN 'both'
            WHEN mobile_amount > 0 THEN 'mobile'
            ELSE 'desktop'
        END AS platform,
        (mobile_amount + desktop_amount) AS amount
    FROM 
        cte
),

cte3 AS (
    SELECT DISTINCT spend_date, 'desktop' AS platform FROM spending
    UNION
    SELECT DISTINCT spend_date, 'mobile' AS platform FROM spending
    UNION
    SELECT DISTINCT spend_date, 'both' AS platform FROM spending
)

SELECT 
    p.spend_date,
    p.platform,
    IFNULL(SUM(o.amount), 0) AS total_amount,
    COUNT(o.user_id) AS total_users
FROM 
    cte3 p
LEFT JOIN 
    cte2 o
ON 
    p.platform = o.platform AND p.spend_date = o.spend_date
GROUP BY 
    p.spend_date, p.platform;
