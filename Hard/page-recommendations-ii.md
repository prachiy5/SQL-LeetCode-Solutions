### Page Recommendations II

You are implementing a page recommendation system for a social media website. Your system will recommend a page to `user_id` if the page is liked by at least one friend of `user_id` and is not liked by `user_id`.

Write a solution to find all the possible page recommendations for every user. Each recommendation should appear as a row in the result table with these columns:

- `user_id`: The ID of the user that your system is making the recommendation to.
- `page_id`: The ID of the page that will be recommended to `user_id`.
- `friends_likes`: The number of the friends of `user_id` that like `page_id`.

### Input Schema

#### Friendship Table

| Column Name | Type |
|-------------|------|
| user1_id    | int  |
| user2_id    | int  |

`(user1_id, user2_id)` is the primary key (combination of columns with unique values) for this table. Each row of this table indicates that the users `user1_id` and `user2_id` are friends.

#### Likes Table

| Column Name | Type |
|-------------|------|
| user_id     | int  |
| page_id     | int  |

`(user_id, page_id)` is the primary key (combination of columns with unique values) for this table. Each row of this table indicates that `user_id` likes `page_id`.

### Output Schema

The result table should contain the following columns:

| Column Name     | Type |
|-----------------|------|
| user_id         | int  |
| page_id         | int  |
| friends_likes   | int  |

The table should be returned in any order.

### Example

#### Input

**Friendship Table:**

| user1_id | user2_id |
|----------|----------|
| 1        | 2        |
| 1        | 3        |
| 1        | 4        |
| 2        | 3        |
| 2        | 4        |
| 2        | 5        |
| 6        | 1        |

**Likes Table:**

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

#### Output

| user_id | page_id | friends_likes |
|---------|---------|---------------|
| 1       | 77      | 2             |
| 1       | 23      | 1             |
| 1       | 24      | 1             |
| 1       | 56      | 1             |
| 1       | 33      | 1             |
| 2       | 24      | 1             |
| 2       | 56      | 1             |
| 2       | 11      | 1             |
| 2       | 88      | 1             |
| 3       | 88      | 1             |
| 3       | 23      | 1             |
| 4       | 88      | 1             |
| 4       | 77      | 1             |
| 4       | 23      | 1             |
| 5       | 77      | 1             |
| 5       | 23      | 1             |

### Thought Process

To generate the required recommendations, we need to:

1. **Identify all friendships:** Generate a table where each row indicates a user and their friend (using a union of `user1_id` and `user2_id` from the `Friendship` table).
2. **Find pages liked by friends:** For each user, determine the pages liked by their friends by joining the friendships with the `Likes` table.
3. **Count the number of friends liking a page:** Group the results by `user_id` and `page_id` to calculate the `friends_likes` for each page.
4. **Exclude pages already liked by the user:** Remove pages from the recommendation list that are already liked by the user.
5. **Return the final results:** Include `user_id`, `page_id`, and `friends_likes` in the result.

### SQL Query
```sql
WITH all_users AS (
    SELECT user1_id AS user_id, user2_id AS friend_id 
    FROM friendship
    UNION
    SELECT user2_id AS user_id, user1_id AS friend_id
    FROM friendship
),

cte2 AS (
    SELECT all_users.*, l.page_id
    FROM all_users 
    LEFT JOIN likes l ON 
        all_users.friend_id = l.user_id
),

cte3 AS (
    SELECT user_id, page_id, COUNT(friend_id) AS friends_likes
    FROM cte2
    GROUP BY user_id, page_id
)

SELECT c.user_id, c.page_id, c.friends_likes 
FROM cte3 c
LEFT JOIN likes l
ON l.user_id = c.user_id AND c.page_id = l.page_id
WHERE l.user_id IS NULL;
