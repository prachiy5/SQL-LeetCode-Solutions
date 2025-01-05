### Page Recommendations
We are given two tables:

#### Table: `Friendship`

| Column Name | Type |
|-------------|------|
| user1_id    | int  |
| user2_id    | int  |

- `(user1_id, user2_id)` is the primary key.
- Each row indicates a friendship relation between `user1_id` and `user2_id`.

#### Table: `Likes`

| Column Name | Type |
|-------------|------|
| user_id     | int  |
| page_id     | int  |

- `(user_id, page_id)` is the primary key.
- Each row indicates that `user_id` likes `page_id`.

### Task
Write a SQL query to recommend pages to the user with `user_id = 1` based on the pages their friends liked. The query should:
1. Recommend pages liked by friends of user `1`.
2. Exclude pages already liked by user `1`.
3. Return a result table with a single column `recommended_page` containing the recommended page IDs.
4. Ensure there are no duplicate recommendations in the result.

### Example
#### Input:
**Friendship table:**

| user1_id | user2_id |
|----------|----------|
| 1        | 2        |
| 1        | 3        |
| 1        | 4        |
| 2        | 3        |
| 2        | 4        |
| 2        | 5        |
| 6        | 1        |

**Likes table:**

| user_id | page_id |
|---------|---------|
| 1       | 88      |
| 2       | 23      |
| 3       | 24      |
| 4       | 56      |
| 5       | 11      |
| 6       | 33      |
| 2       | 77      |
| 3       | 77      |
| 6       | 88      |

#### Output:

| recommended_page |
|------------------|
| 23               |
| 24               |
| 56               |
| 33               |
| 77               |

#### Explanation:
- User `1` is friends with users `2`, `3`, `4`, and `6`.
- Pages liked by friends are:
  - User `2`: Pages `23` and `77`
  - User `3`: Pages `24` and `77`
  - User `4`: Page `56`
  - User `6`: Pages `33` and `88`
- Exclude pages already liked by user `1` (`88`).
- The recommended pages are `23`, `24`, `56`, `33`, and `77` (removing duplicates).

---

### Thought Process
1. **Find Friends of User 1:**
   - Use the `Friendship` table to get all friends of user `1` (`user1_id` or `user2_id` equal to `1`).
2. **Find Pages Liked by Friends:**
   - Use the `Likes` table to find pages liked by these friends.
3. **Exclude Pages Already Liked by User 1:**
   - Use a subquery to filter out pages that user `1` already likes.
4. **Remove Duplicates:**
   - Use `DISTINCT` to ensure the result contains unique recommended pages.

---

### SQL Query
```sql
WITH cte AS (
    SELECT user2_id AS user_friends 
    FROM Friendship 
    WHERE user1_id = 1 
    UNION 
    SELECT user1_id AS user_friends 
    FROM Friendship
    WHERE user2_id = 1
)

SELECT DISTINCT page_id AS recommended_page 
FROM Likes
WHERE user_id IN (SELECT user_friends FROM cte)
  AND page_id NOT IN (SELECT page_id FROM Likes WHERE user_id = 1);
```

---

### Key Takeaways
1. **Friendship Relationship:** Use `UNION` to find all friends of a user, regardless of whether they are listed in `user1_id` or `user2_id`.
2. **Exclude Pages:** Use `NOT IN` to filter out pages already liked by the user.
3. **Remove Duplicates:** Use `DISTINCT` to ensure each recommended page appears only once.
4. **Subqueries:** Use subqueries to dynamically filter data (e.g., friends and pages).

### Related Topics
- Self Joins in SQL
- Subqueries and Filtering
- Using `UNION` for Merging Data
- Removing Duplicates with `DISTINCT`
