# Number of Comments Per Post

### Problem

#### Table Overview

**Submissions**

| Column Name | Type     |
|-------------|----------|
| sub_id      | int      |
| parent_id   | int      |

- Each row in the table can be a **post** or a **comment**.
- `parent_id` is:
  - `NULL` for **posts**.
  - The `sub_id` of the corresponding **post** for **comments**.

#### Task
Find the number of **unique comments** for each **post**. Posts with no comments should have a `number_of_comments` value of `0`. Ensure that:
1. Duplicate posts are treated as one post.
2. Duplicate comments are only counted once per post.
3. Results are ordered by `post_id` in ascending order.

---

### Example

**Input:**

`Submissions` table:

| sub_id | parent_id |
|--------|-----------|
| 1      | NULL      |
| 2      | NULL      |
| 1      | NULL      |
| 12     | NULL      |
| 3      | 1         |
| 5      | 2         |
| 3      | 1         |
| 4      | 1         |
| 9      | 1         |
| 10     | 2         |
| 6      | 7         |

**Output:**

| post_id | number_of_comments |
|---------|--------------------|
| 1       | 3                  |
| 2       | 2                  |
| 12      | 0                  |

**Explanation:**
- **Post 1:**
  - Comments: `3`, `4`, `9` (comment `3` is counted only once).
  - Unique comments = 3.
- **Post 2:**
  - Comments: `5`, `10`.
  - Unique comments = 2.
- **Post 12:**
  - No comments.
  - Unique comments = 0.

---

### Thought Process

1. **Filter for Posts:**
   - Identify rows where `parent_id` is `NULL` to extract posts.

2. **Join to Find Comments:**
   - Use a **LEFT JOIN** to link posts (`s1.sub_id`) with their comments (`s2.parent_id`).

3. **Count Unique Comments:**
   - Use `COUNT(DISTINCT s2.sub_id)` to count unique comments for each post.

4. **Handle Posts Without Comments:**
   - The `LEFT JOIN` ensures that posts without comments will have a `NULL` value for `s2.sub_id`, which will result in a count of `0`.

5. **Group and Sort:**
   - Group by `s1.sub_id` (post ID) to calculate the count for each post.
   - Order the results by `post_id` in ascending order.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
    s1.sub_id AS post_id,
    COUNT(DISTINCT s2.sub_id) AS number_of_comments
FROM Submissions s1
LEFT JOIN Submissions s2
    ON s1.sub_id = s2.parent_id
WHERE s1.parent_id IS NULL
GROUP BY s1.sub_id
ORDER BY s1.sub_id;
```

---

### Solution Explanation

1. **Filter for Posts:**
   - `s1.parent_id IS NULL` ensures that only rows corresponding to posts are included.

2. **Join to Find Comments:**
   - Use `LEFT JOIN` to connect posts (`s1`) with their comments (`s2`) based on `s1.sub_id = s2.parent_id`.

3. **Count Unique Comments:**
   - `COUNT(DISTINCT s2.sub_id)` ensures duplicate comments are only counted once for each post.

4. **Group and Sort:**
   - Group by `s1.sub_id` to calculate metrics for each post.
   - Order the results by `post_id` to provide a structured output.

---

### Key Takeaways

- **LEFT JOIN:** Essential for including rows that may not have matching entries in the joined table (e.g., posts without comments).
- **DISTINCT:** Prevents duplicate rows from being included in the count.
- **NULL Handling:** Ensures posts with no comments are represented correctly with a count of `0`.

---

### Related Topics
- SQL Joins and Aggregations
- Handling NULL Values in SQL
- Counting Unique Values in SQL
