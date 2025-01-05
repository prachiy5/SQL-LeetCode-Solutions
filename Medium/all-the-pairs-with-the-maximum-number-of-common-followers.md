### All the Pairs With the Maximum Number of Common Followers

**Schema**:

Table: Relations

```sql
+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| follower_id | int  |
+-------------+------+
```

- `(user_id, follower_id)` is the primary key (combination of columns with unique values) for this table.
- Each row of this table indicates that the user with ID `follower_id` is following the user with ID `user_id`.

**Task**:
Write a solution to find all the pairs of users with the maximum number of common followers. In other words, if the maximum number of common followers between any two users is `maxCommon`, then you have to return all pairs of users that have `maxCommon` common followers.

The result table should contain the pairs `user1_id` and `user2_id` where `user1_id < user2_id`.

Return the result table in any order.

---

### Example

**Input**:

Relations table:

```sql
+---------+-------------+
| user_id | follower_id |
+---------+-------------+
| 1       | 3           |
| 2       | 3           |
| 7       | 3           |
| 1       | 4           |
| 2       | 4           |
| 7       | 4           |
| 1       | 5           |
| 2       | 6           |
| 7       | 5           |
+---------+-------------+
```

**Output**:

```sql
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 7        |
+----------+----------+
```

**Explanation**:

- Users 1 and 2 have two common followers (3 and 4).
- Users 1 and 7 have three common followers (3, 4, and 5).
- Users 2 and 7 have two common followers (3 and 4).

Since the maximum number of common followers between any two users is `3`, we return all pairs of users with three common followers, which is only the pair `(1, 7)`. We return the pair as `(1, 7)`, not as `(7, 1)`.

---

### Thought Process

1. **Understand the Problem**:
   - We need to find all pairs of users that have the maximum number of common followers.
   - The result should only include pairs where `user1_id < user2_id`.

2. **Plan the Solution**:
   - Use a self-join on the `Relations` table to compare followers of different users.
   - Count the number of common followers for each pair of users.
   - Filter the pairs to only include those with the maximum count of common followers.

3. **Steps**:
   - Perform a self-join on the `Relations` table, ensuring `user1_id < user2_id`.
   - Count the number of common followers for each pair of users.
   - Identify the maximum count of common followers.
   - Filter and return pairs of users with the maximum count.

---

### SQL Query

```sql
WITH cte AS (
    SELECT 
        u1.user_id AS user1_id, 
        u2.user_id AS user2_id,
        COUNT(*) AS cnt
    FROM relations u1
    JOIN relations u2
        ON u1.user_id < u2.user_id
        AND u1.follower_id = u2.follower_id
    GROUP BY u1.user_id, u2.user_id
)

SELECT 
    u.user1_id, 
    u.user2_id 
FROM cte u
WHERE u.cnt = (SELECT MAX(cnt) FROM cte);
