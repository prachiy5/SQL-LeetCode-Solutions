### Second Degree Follower

**Table:** Follow

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| followee    | varchar |
| follower    | varchar |
+-------------+---------+
```

- `(followee, follower)` is the primary key (combination of columns with unique values) for this table.
- Each row of this table indicates that the user `follower` follows the user `followee` on a social network.
- There will not be a user following themself.

A second-degree follower is a user who:

1. Follows at least one user, and
2. Is followed by at least one user.

Write a solution to report the second-degree users and the number of their followers.

**Return the result table ordered by `follower` in alphabetical order.**

### Example

#### Input:

**Follow** table:

```
+----------+----------+
| followee | follower |
+----------+----------+
| Alice    | Bob      |
| Bob      | Cena     |
| Bob      | Donald   |
| Donald   | Edward   |
+----------+----------+
```

#### Output:

```
+----------+-----+
| follower | num |
+----------+-----+
| Bob      | 2   |
| Donald   | 1   |
+----------+-----+
```

#### Explanation:
- User `Bob` has 2 followers. Bob is a second-degree follower because he follows Alice, so we include him in the result table.
- User `Donald` has 1 follower. Donald is a second-degree follower because he follows Bob, so we include him in the result table.
- User `Alice` has 1 follower. Alice is not a second-degree follower because she does not follow anyone, so we do not include her in the result table.

---

### Thought Process

1. Identify users who are **followees** (users who are followed by someone) and calculate the count of their followers.
2. Ensure that these users are also **followers** (they follow at least one user).
3. Filter out users who are not both followees and followers.
4. Return the `follower` and the number of their followers (`num`), ordered alphabetically by `follower`.

---

### Query

```sql
SELECT DISTINCT followee AS follower, COUNT(follower) AS num
FROM follow
GROUP BY followee
HAVING COUNT(follower) >= 1
  AND followee IN (
      SELECT follower
      FROM follow
      GROUP BY follower
      HAVING COUNT(followee) >= 1
  )
ORDER BY follower;
