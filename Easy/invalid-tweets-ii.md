### 3150. Invalid Tweets II

**Table: Tweets**

```plaintext
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| tweet_id       | int     |
| content        | varchar |
+----------------+---------+
```
- `tweet_id` is the primary key (column with unique values) for this table.
- This table contains all the tweets in a social media app.

**Write a solution to find invalid tweets.**
A tweet is considered invalid if it meets any of the following criteria:

1. It exceeds 140 characters in length.
2. It has more than 3 mentions.
3. It includes more than 3 hashtags.

**Return the result table ordered by `tweet_id` in ascending order.**

### Example:

**Input:**

**Tweets table:**

```plaintext
+----------+-----------------------------------------------------------------------------------+
| tweet_id | content                                                                           |
+----------+-----------------------------------------------------------------------------------+
| 1        | Traveling, exploring, and living my best life @JaneSmith @SaraJohnson @LisaTaylor |
|          | @MikeBrown #Foodie #Fitness #Learning                                             | 
| 2        | Just had the best dinner with friends! #Foodie #Friends #Fun                      |
| 4        | Working hard on my new project #Work #Goals #Productivity #Fun                    |
+----------+-----------------------------------------------------------------------------------+
```

**Output:**

```plaintext
+----------+
| tweet_id |
+----------+
| 1        |
| 4        |
+----------+
```

### Explanation:
- `tweet_id` 1 contains 4 mentions.
- `tweet_id` 4 contains 4 hashtags.

The output table is ordered by `tweet_id` in ascending order.

---

### Thought Process:

1. **Understand the Criteria for Invalid Tweets:**
   - Tweets exceeding 140 characters are invalid.
   - Tweets containing more than 3 mentions ("@").
   - Tweets containing more than 3 hashtags ("#").

2. **Approach:**
   - Use a Common Table Expression (CTE) to calculate:
     - The length of the tweet content.
     - The number of mentions (count of "@" symbols).
     - The number of hashtags (count of "#" symbols).
   - Filter tweets that violate any of the given criteria.

3. **Result:**
   - Return the `tweet_id` of invalid tweets ordered in ascending order.

---

### Query:

```sql
WITH CTE AS (
  SELECT
    tweet_id,
    LENGTH(content) AS content_len,
    LENGTH(content) - LENGTH(REPLACE(content, '@', '')) AS mention_count,
    LENGTH(content) - LENGTH(REPLACE(content, '#', '')) AS hashtag_count
  FROM tweets
)
SELECT tweet_id
FROM CTE
WHERE 
  content_len > 140 OR
  mention_count > 3 OR
  hashtag_count > 3
ORDER BY tweet_id;
```

---

### Explanation of the Query:

1. **CTE Calculation:**
   - `content_len`: Calculates the total length of the tweet content.
   - `mention_count`: Counts the number of "@" symbols in the content by subtracting the length of the content after removing all "@" symbols.
   - `hashtag_count`: Counts the number of "#" symbols similarly.

2. **Filter Invalid Tweets:**
   - The `WHERE` clause filters tweets that exceed the character limit, contain more than 3 mentions, or have more than 3 hashtags.

3. **Order the Results:**
   - The results are ordered by `tweet_id` in ascending order to meet the requirements.

---

### Output:
For the provided example input, this query will return:

```plaintext
+----------+
| tweet_id |
+----------+
| 1        |
| 4        |
+----------+
