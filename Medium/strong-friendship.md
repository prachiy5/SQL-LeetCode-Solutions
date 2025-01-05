
### Strong Friendship

Table: Friendship

```sql
+-------------+------+
| Column Name | Type |
+-------------+------+
| user1_id    | int  |
| user2_id    | int  |
+-------------+------+
(user1_id, user2_id) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates that the users user1_id and user2_id are friends.
Note that user1_id < user2_id.
```

A friendship between a pair of friends `x` and `y` is strong if `x` and `y` have at least three common friends.

Write a solution to find all the strong friendships.

Note that the result table should not contain duplicates with `user1_id < user2_id`.

Return the result table in any order.

The result format is in the following example:

### Example Input:

**Friendship table:**

```sql
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
| 1        | 3        |
| 2        | 3        |
| 1        | 4        |
| 2        | 4        |
| 1        | 5        |
| 2        | 5        |
| 1        | 7        |
| 3        | 7        |
| 1        | 6        |
| 3        | 6        |
| 2        | 6        |
+----------+----------+
```

### Example Output:

```sql
+----------+----------+---------------+
| user1_id | user2_id | common_friend |
+----------+----------+---------------+
| 1        | 2        | 4             |
| 1        | 3        | 3             |
+----------+----------+---------------+
```

### Explanation:

- Users `1` and `2` have `4` common friends (`3`, `4`, `5`, and `6`).
- Users `1` and `3` have `3` common friends (`2`, `6`, and `7`).
- The friendship between users `2` and `3` is not included because they only have `2` common friends (`1` and `6`).

---
### Thought Process

1. **Union for Symmetry**: Start by creating a unified table where each friendship is bidirectional, i.e., both `(user1_id, user2_id)` and `(user2_id, user1_id)` are included.
2. **Join for Common Friends**: Perform a self-join on the unified table where the `friend_id` of one user matches the `friend_id` of another user.
3. **Filter Strong Friendships**: Group the results by `user1_id` and `user2_id` and count the number of common friends. Filter for groups with `3` or more common friends.
4. **Validation**: Ensure that the result matches the original friendship structure (i.e., the order `user1_id < user2_id` is maintained).

---
### Query Solution

```sql
WITH cte AS (
    SELECT user1_id AS user_id, user2_id AS friend_id FROM friendship
    UNION
    SELECT user2_id AS user_id, user1_id AS friend_id FROM friendship
),

cte2 AS (
    SELECT 
        c1.user_id AS user1_id,
        c2.user_id AS user2_id,
        COUNT(c2.friend_id) AS common_friend
    FROM cte c1
    LEFT JOIN cte c2
        ON c1.friend_id = c2.friend_id
        AND c1.user_id < c2.user_id
    GROUP BY c1.user_id, c2.user_id
    HAVING COUNT(c2.friend_id) >= 3
)

SELECT * 
FROM cte2
WHERE (user1_id, user2_id) IN (SELECT * FROM friendship);
