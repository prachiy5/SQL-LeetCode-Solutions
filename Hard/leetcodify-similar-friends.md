### Leetcodify Similar Friends

We have two tables:

#### Table: Listens

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| song_id     | int     |
| day         | date    |

This table may contain duplicate rows. Each row of this table indicates that the user `user_id` listened to the song `song_id` on the day `day`.

#### Table: Friendship

| Column Name | Type    |
|-------------|---------|
| user1_id    | int     |
| user2_id    | int     |

`(user1_id, user2_id)` is the primary key for this table. Each row of this table indicates that the users `user1_id` and `user2_id` are friends. Note that `user1_id < user2_id`.

Write a solution to report the similar friends of Leetcodify users. A user `x` and user `y` are similar friends if:

1. Users `x` and `y` are friends, and
2. Users `x` and `y` listened to the same three or more different songs on the same day.

Return the result table in any order. Note that you must return the similar pairs of friends the same way they were represented in the input (i.e., always `user1_id < user2_id`).

#### Output Format

| Column Name | Type |
|-------------|------|
| user1_id    | int  |
| user2_id    | int  |

### Example

#### Input:

**Listens table:**

| user_id | song_id | day        |
|---------|---------|------------|
| 1       | 10      | 2021-03-15 |
| 1       | 11      | 2021-03-15 |
| 1       | 12      | 2021-03-15 |
| 2       | 10      | 2021-03-15 |
| 2       | 11      | 2021-03-15 |
| 2       | 12      | 2021-03-15 |
| 3       | 10      | 2021-03-15 |
| 3       | 11      | 2021-03-15 |
| 3       | 12      | 2021-03-15 |
| 4       | 10      | 2021-03-15 |
| 4       | 11      | 2021-03-15 |
| 4       | 13      | 2021-03-15 |
| 5       | 10      | 2021-03-16 |
| 5       | 11      | 2021-03-16 |
| 5       | 12      | 2021-03-16 |

**Friendship table:**

| user1_id | user2_id |
|----------|----------|
| 1        | 2        |
| 2        | 4        |
| 2        | 5        |

#### Output:

| user1_id | user2_id |
|----------|----------|
| 1        | 2        |

### Thought Process

To solve this problem, we need to:

1. Identify pairs of users who listened to the same three or more different songs on the same day.
2. Filter these pairs to retain only those who are friends as per the `Friendship` table.
3. Ensure that the output is ordered with `user1_id < user2_id`.

#### Steps:

1. **Join Listens table with itself:**
   - Use a self-join on the `Listens` table to find pairs of users (`user1`, `user2`) who listened to the same song on the same day.
   - Group by `user1`, `user2`, and `day`.
   - Filter groups where the count of distinct `song_id` is 3 or more.

2. **Join with Friendship table:**
   - Filter the pairs of users identified in step 1 to include only those who are friends in the `Friendship` table.

3. **Output the result:**
   - Select `user1_id` and `user2_id` from the filtered pairs.

### Query

```sql
WITH cte AS (
    SELECT 
        l1.user_id AS user1, 
        l2.user_id AS user2,
        l1.day
    FROM listens l1
    INNER JOIN listens l2
        ON l1.song_id = l2.song_id
        AND l1.day = l2.day
    WHERE l1.user_id < l2.user_id
    GROUP BY l1.user_id, l2.user_id, l1.day
    HAVING COUNT(DISTINCT l1.song_id) >= 3
),

cte2 AS (
    SELECT 
        user1, 
        user2
    FROM cte 
    INNER JOIN friendship f
        ON cte.user1 = f.user1_id 
        AND cte.user2 = f.user2_id
)

SELECT DISTINCT 
    user1 AS user1_id, 
    user2 AS user2_id
FROM cte2;
