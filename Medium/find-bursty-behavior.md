### Find Bursty Behavior

#### Table: Posts
| Column Name | Type    |
|-------------|---------|
| post_id     | int     |
| user_id     | int     |
| post_date   | date    |

- `post_id` is the primary key (column with unique values) for this table.
- Each row contains `post_id`, `user_id`, and `post_date`.

#### Task
Write a solution to find users who demonstrate **bursty behavior** in their posting patterns during February 2024. **Bursty behavior** is defined as any period of 7 consecutive days where a user's posting frequency is at least twice their average weekly posting frequency for February 2024.

- Only include the dates from February 1 to February 28 in your analysis.
- Consider February as having exactly 4 weeks.

#### Return Format
Return the result table ordered by `user_id` in ascending order, with the following columns:
- `user_id`: The ID of the user.
- `max_7day_posts`: The maximum number of posts made by the user in any 7-day period.
- `avg_weekly_posts`: The average number of posts made by the user per week in February 2024.

---

### Example

#### Input:
**Posts Table:**
| post_id | user_id | post_date  |
|---------|---------|------------|
| 1       | 1       | 2024-02-27 |
| 2       | 5       | 2024-02-06 |
| 3       | 3       | 2024-02-25 |
| 4       | 3       | 2024-02-14 |
| 5       | 3       | 2024-02-06 |
| 6       | 2       | 2024-02-25 |

#### Output:
| user_id | max_7day_posts | avg_weekly_posts |
|---------|----------------|------------------|
| 1       | 1              | 0.2500           |
| 2       | 1              | 0.2500           |
| 5       | 1              | 0.2500           |

#### Explanation:
- **User 1**: Made only 1 post in February, resulting in an average of 0.25 posts per week and a max of 1 post in any 7-day period.
- **User 2**: Also made just 1 post, with the same average and max 7-day posting frequency as User 1.
- **User 5**: Like Users 1 and 2, User 5 made only 1 post throughout February, leading to the same average and max 7-day posting metrics.
- **User 3**: Although User 3 made more posts than the others (3 posts), they did not reach twice the average weekly posts in their consecutive 7-day window, so they are not listed in the output.

---

### Thought Process
1. **Filter Posts for February 2024:** Restrict the data to posts made between February 1, 2024, and February 28, 2024.
2. **Calculate Average Weekly Posts:** Divide the total number of posts by 4 (since February 2024 has 4 weeks).
3. **Compute 7-Day Posting Frequency:** Use a window function with a 7-day sliding range to count the number of posts in any 7-day period for each user.
4. **Identify Bursty Users:** Filter users whose maximum 7-day posting frequency is at least twice their average weekly posting frequency.
5. **Format Output:** Display `user_id`, `max_7day_posts`, and `avg_weekly_posts`, ordered by `user_id`.

---

### Query
```sql
WITH cte AS (
    SELECT 
        *, 
        COUNT(*) OVER(PARTITION BY user_id) / 4.0 AS avg_weekly_posts
    FROM Posts
    WHERE post_date BETWEEN '2024-02-01' AND '2024-02-28'
),

cte2 AS (
    SELECT 
        *, 
        COUNT(post_id) OVER(
            PARTITION BY user_id 
            ORDER BY post_date 
            RANGE BETWEEN INTERVAL 6 DAY PRECEDING AND CURRENT ROW
        ) AS max_7day_posts
    FROM cte
)

SELECT DISTINCT 
    user_id, 
    MAX(max_7day_posts) AS max_7day_posts, 
    avg_weekly_posts
FROM cte2
WHERE max_7day_posts >= 2 * avg_weekly_posts
GROUP BY user_id, avg_weekly_posts
ORDER BY user_id;
