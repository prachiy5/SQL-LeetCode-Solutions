### Find Trending Hashtags II 

Table: Tweets

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| tweet_id    | int     |
| tweet_date  | date    |
| tweet       | varchar |

- `tweet_id` is the primary key (column with unique values) for this table.
- Each row of this table contains `user_id`, `tweet_id`, `tweet_date`, and `tweet`.
- It is guaranteed that all `tweet_date` are valid dates in February 2024.

Write a solution to find the top 3 trending hashtags in February 2024. Every tweet may contain several hashtags.

Return the result table ordered by `count` of hashtag and `hashtag` in descending order.

---

### Example:

#### Input:

Tweets table:

| user_id | tweet_id | tweet                                                      | tweet_date |
|---------|----------|------------------------------------------------------------|------------|
| 135     | 13       | Enjoying a great start to the day. #HappyDay #MorningVibes | 2024-02-01 |
| 136     | 14       | Another #HappyDay with good vibes! #FeelGood               | 2024-02-03 |
| 137     | 15       | Productivity peaks! #WorkLife #ProductiveDay               | 2024-02-04 |
| 138     | 16       | Exploring new tech frontiers. #TechLife #Innovation        | 2024-02-04 |
| 139     | 17       | Gratitude for today's moments. #HappyDay #Thankful         | 2024-02-05 |
| 140     | 18       | Innovation drives us. #TechLife #FutureTech                | 2024-02-07 |
| 141     | 19       | Connecting with nature's serenity. #Nature #Peaceful       | 2024-02-09 |

#### Output:

| hashtag   | count |
|-----------|-------|
| #HappyDay | 3     |
| #TechLife | 2     |
| #WorkLife | 1     |

#### Explanation:
- `#HappyDay` appeared in tweet IDs 13, 14, and 17, with a total count of 3 mentions.
- `#TechLife` appeared in tweet IDs 16 and 18, with a total count of 2 mentions.
- `#WorkLife` appeared in tweet ID 15, with a total count of 1 mention.
- The output table is sorted in descending order by `count` and `hashtag` respectively.

---

### Thought Process:
1. **Extract Hashtags**:
   - Use regular expressions to extract hashtags from the `tweet` column. Since each tweet may contain multiple hashtags, we need to recursively extract all hashtags from each tweet.

2. **Count Hashtag Frequency**:
   - Group by hashtags and count their occurrences.

3. **Sort and Limit**:
   - Sort the hashtags by their count in descending order. If there are ties, sort alphabetically by the hashtag.
   - Use `LIMIT 3` to return only the top 3 hashtags.

4. **Return Results**:
   - Output the `hashtag` and its `count`.

---

### Query:

```sql
WITH RECURSIVE tags AS (
    SELECT 
        REGEXP_SUBSTR(tweet, '#[^\s]+') AS hashtag,
        REGEXP_REPLACE(tweet, '#[^\s]+', '', 1, 1) AS tweet
    FROM 
        tweets
    UNION ALL
    SELECT 
        REGEXP_SUBSTR(tweet, '#[^\s]+') AS hashtag,
        REGEXP_REPLACE(tweet, '#[^\s]+', '', 1, 1) AS tweet
    FROM 
        tags
    WHERE 
        hashtag IS NOT NULL
)

SELECT 
    hashtag,
    COUNT(*) AS count
FROM 
    tags
WHERE 
    hashtag IS NOT NULL
GROUP BY 
    hashtag
ORDER BY 
    count DESC, 
    hashtag DESC
LIMIT 3;
