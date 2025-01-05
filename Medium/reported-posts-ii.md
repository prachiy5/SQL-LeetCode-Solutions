### Reported Posts II
We are given two tables:

#### Table: `Actions`

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| post_id       | int     |
| action_date   | date    |
| action        | enum    |
| extra         | varchar |

- The table may have duplicate rows.
- The `action` column is an ENUM type with possible values: `('view', 'like', 'reaction', 'comment', 'report', 'share')`.
- The `extra` column contains optional information, such as reasons for reports.

#### Table: `Removals`

| Column Name   | Type |
|---------------|------|
| post_id       | int  |
| remove_date   | date |

- `post_id` is the primary key.
- Each row indicates a post that was removed, either because it was reported or due to an admin review.

### Task
Write a SQL query to calculate the **average daily percentage** of posts that were removed after being reported as spam. The result should be rounded to two decimal places and returned as a single number in a column named `average_daily_percent`.

### Example
#### Input:
**Actions table:**

| user_id | post_id | action_date | action | extra  |
|---------|---------|-------------|--------|--------|
| 1       | 1       | 2019-07-01  | view   | null   |
| 1       | 1       | 2019-07-01  | like   | null   |
| 1       | 1       | 2019-07-01  | share  | null   |
| 2       | 2       | 2019-07-04  | view   | null   |
| 2       | 2       | 2019-07-04  | report | spam   |
| 3       | 4       | 2019-07-04  | view   | null   |
| 3       | 4       | 2019-07-04  | report | spam   |
| 4       | 3       | 2019-07-02  | view   | null   |
| 4       | 3       | 2019-07-02  | report | spam   |
| 5       | 2       | 2019-07-03  | view   | null   |
| 5       | 2       | 2019-07-03  | report | racism |
| 5       | 5       | 2019-07-03  | view   | null   |
| 5       | 5       | 2019-07-03  | report | racism |

**Removals table:**

| post_id | remove_date |
|---------|-------------|
| 2       | 2019-07-20  |
| 3       | 2019-07-18  |

#### Output:

| average_daily_percent |
|-----------------------|
| 75.00                 |

#### Explanation:
- On `2019-07-04`, two posts were reported as spam (`post_id = 2` and `post_id = 4`), but only one (`post_id = 2`) was removed. The percentage for this day is 50%.
- On `2019-07-02`, one post (`post_id = 3`) was reported as spam and removed. The percentage for this day is 100%.
- The other days did not have any spam reports, so they are ignored.
- The average percentage is calculated as `(50 + 100) / 2 = 75.00%`.

---

### Thought Process
1. **Filter Relevant Actions**: Focus only on rows where `action = 'report'` and `extra = 'spam'`.
2. **Join Tables**: Perform a `LEFT JOIN` between `Actions` and `Removals` on `post_id` to check if reported posts were removed.
3. **Calculate Daily Removal Rate**: For each `action_date`, compute the percentage of spam-reported posts that were removed (`count(removed_posts) / count(reported_posts)`).
4. **Calculate Average Percentage**: Take the average of the daily percentages and round the result to two decimal places.

---

### SQL Query
```sql
WITH cte AS (
    SELECT 
        a.post_id,
        a.action_date,
        a.action,
        COUNT(DISTINCT r.post_id) / COUNT(DISTINCT a.post_id) AS daily_rate 
    FROM Actions a
    LEFT JOIN Removals r
    ON a.post_id = r.post_id
    WHERE a.action = 'report'
      AND a.extra = 'spam'
    GROUP BY a.action_date
)

SELECT 
    ROUND(AVG(daily_rate) * 100, 2) AS average_daily_percent
FROM cte;
```

---

### Key Takeaways
1. **Filter Specific Actions**: Use a `WHERE` clause to narrow down the dataset to spam-related reports.
2. **Join for Additional Information**: Use `LEFT JOIN` to incorporate removal information for reported posts.
3. **Daily Rate Calculation**: Use `COUNT(DISTINCT)` to compute the number of spam-reported posts and removed posts per day.
4. **Aggregate Results**: Use `AVG()` to calculate the overall average and `ROUND()` to format the result.

### Related Topics
- Filtering with `WHERE`
- Joining Tables
- Aggregation with `GROUP BY`
- Window Functions and CTEs
