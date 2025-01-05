### Exchange Seats

**Table: Follow**

```plaintext
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| followee    | varchar |
| follower    | varchar |
+-------------+---------+
(followee, follower) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates that the user follower follows the user followee on a social network.
There will not be a user following themself.
```

A second-degree follower is a user who:

1. Follows at least one user, and
2. Is followed by at least one user.

Write a solution to report the second-degree users and the number of their followers.

**Return the result table ordered by `follower` in alphabetical order.**

### Example 1:

**Input:** 

**Follow table:**

```plaintext
+----------+----------+
| followee | follower |
+----------+----------+
| Alice    | Bob      |
| Bob      | Cena     |
| Bob      | Donald   |
| Donald   | Edward   |
+----------+----------+
```

**Output:**

```plaintext
+----------+-----+
| follower | num |
+----------+-----+
| Bob      | 2   |
| Donald   | 1   |
+----------+-----+
```

**Explanation:**
- User Bob has 2 followers. Bob is a second-degree follower because he follows Alice, so we include him in the result table.
- User Donald has 1 follower. Donald is a second-degree follower because he follows Bob, so we include him in the result table.
- User Alice has 1 follower. Alice is not a second-degree follower because she does not follow anyone, so we do not include her in the result table.

---

### Thought Process

1. **Identify second-degree users:**
   - A second-degree user must meet two criteria:
     - They follow at least one other user (`followee`).
     - They are followed by at least one user (`follower`).

2. **Steps to Solve:**
   - Count the followers (`num`) for each user who is a `followee` (i.e., someone who is followed by others).
   - Filter those users who are also present as a `follower` (i.e., they follow someone).
   - Order the results alphabetically by the `follower` column.

3. **Output:**
   - Include the `follower` name and their number of followers (`num`).

---

### Query

```sql
SELECT DISTINCT followee AS follower, COUNT(follower) AS num
FROM Follow
GROUP BY followee
HAVING COUNT(follower) >= 1
  AND followee IN (
      SELECT follower
      FROM Follow
      GROUP BY follower
      HAVING COUNT(followee) >= 1
  )
ORDER BY follower;
```

### Explanation of the Query

1. **Count Followers:**
   - `GROUP BY followee` and `COUNT(follower)` calculate the number of followers for each user.

2. **Filter Second-Degree Users:**
   - The `HAVING` clause ensures that:
     - The user (`followee`) has at least one follower (`COUNT(follower) >= 1`).
     - The user is also a `follower` in the `Follow` table, verified using a subquery.

3. **Ordering:**
   - Results are ordered alphabetically by `follower`.

---

### Output

For the given input example, the output will be:

```plaintext
+----------+-----+
| follower | num |
+----------+-----+
| Bob      | 2   |
| Donald   | 1   |
+----------+-----+
