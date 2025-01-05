### Find Trending Hashtags

We are tasked to find the top 3 trending hashtags in February 2024 from a table of tweets. Each tweet contains exactly one hashtag. The result should include the hashtag and the number of times it appears, ordered by the count of occurrences in descending order. If there is a tie in the count, the hashtags should be ordered alphabetically.

---

### Table Schema

#### Table: Tweets

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| tweet_id    | int     |
| tweet_date  | date    |
| tweet       | varchar |

- `tweet_id` is the primary key.
- Each row contains `user_id`, `tweet_id`, `tweet_date`, and `tweet`.
- Each tweet contains exactly one hashtag.

---

### Example Input/Output

#### Input:
**Tweets table:**

| user_id | tweet_id | tweet                                        | tweet_date |
|---------|----------|----------------------------------------------|------------|
| 135     | 13       | Enjoying a great start to the day! #HappyDay | 2024-02-01 |
| 136     | 14       | Another #HappyDay with good vibes!           | 2024-02-03 |
| 137     | 15       | Productivity peaks! #WorkLife                | 2024-02-04 |
| 138     | 16       | Exploring new tech frontiers. #TechLife      | 2024-02-04 |
| 139     | 17       | Gratitude for today's moments. #HappyDay     | 2024-02-05 |
| 140     | 18       | Innovation drives us. #TechLife              | 2024-02-07 |
| 141     | 19       | Connecting with nature's serenity. #Nature   | 2024-02-09 |

#### Output:
| hashtag   | hashtag_count |
|-----------|---------------|
| #HappyDay | 3             |
| #TechLife | 2             |
| #WorkLife | 1             |

#### Explanation:
- **#HappyDay:** Appeared in tweet IDs 13, 14, and 17, with a total count of 3 mentions.
- **#TechLife:** Appeared in tweet IDs 16 and 18, with a total count of 2 mentions.
- **#WorkLife:** Appeared in tweet ID 15, with a total count of 1 mention.
- The output is sorted in descending order by `hashtag_count`, and alphabetically by `hashtag` for ties.

---

### Thought Process
1. **Extract Hashtags:**
   - Use string functions to extract the hashtag from the `tweet` column.
   - Locate the `#` symbol and extract the substring until the first space after the hashtag.

2. **Filter for February 2024:**
   - Include only rows where `tweet_date` falls between `2024-02-01` and `2024-02-29`.

3. **Group and Count:**
   - Group by `hashtag` and count the occurrences for each hashtag.

4. **Order and Limit Results:**
   - Sort by `hashtag_count` in descending order.
   - In case of ties, sort alphabetically by `hashtag`.
   - Limit the result to the top 3 hashtags.

---

### Query
```sql
WITH cte AS (
    SELECT 
        user_id,
        tweet_id,
        SUBSTRING_INDEX(SUBSTRING(tweet, LOCATE('#', tweet)), ' ', 1) AS hashtag,
        tweet_date
    FROM tweets
)

SELECT 
    hashtag,
    COUNT(user_id) AS hashtag_count
FROM cte
WHERE tweet_date BETWEEN '2024-02-01' AND '2024-02-29'
GROUP BY hashtag
ORDER BY hashtag_count DESC, hashtag DESC
LIMIT 3;
```

---

### Key Takeaways
- **String Manipulation:** Use `LOCATE` and `SUBSTRING_INDEX` to extract substrings from text efficiently.
- **Filtering by Date:** Ensure correct filtering with a specific date range.
- **Grouping and Counting:** Aggregating data using `GROUP BY` and counting occurrences.
- **Ordering and Limiting:** Sorting results based on multiple criteria and limiting output for better performance and relevance.

---

### Related Topics
- String Functions in SQL
- Aggregate Functions
- Common Table Expressions (CTEs)
- Sorting and Filtering Data
