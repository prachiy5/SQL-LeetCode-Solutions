### Friend Requests II: Who Has the Most Friends

**Table: RequestAccepted**

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| requester_id   | int     |
| accepter_id    | int     |
| accept_date    | date    |
+----------------+---------+
```
- `(requester_id, accepter_id)` is the primary key (combination of columns with unique values) for this table.
- This table contains the ID of the user who sent the request, the ID of the user who received the request, and the date when the request was accepted.

---

**Task:**
Write a solution to find the person who has the most friends and the total number of friends they have.

The test cases are generated so that only one person has the most friends.

---

### Example:

**Input:**

RequestAccepted table:

```
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
+--------------+-------------+-------------+
```

**Output:**

```
+----+-----+
| id | num |
+----+-----+
| 3  | 3   |
+----+-----+
```

**Explanation:**
The person with ID `3` is a friend of people `1`, `2`, and `4`, so they have `3` friends in total, which is the highest among all users.

---

### Thought Process:

1. **Understand the Problem:**
   - Each row in the `RequestAccepted` table represents a friendship between the `requester_id` and `accepter_id`.
   - Both `requester_id` and `accepter_id` contribute to the total number of friends for each user.

2. **Combine All Friendships:**
   - Use a `UNION ALL` to combine all `requester_id` and `accepter_id` into one column. This ensures every user appears for every friendship.

3. **Count Friends for Each User:**
   - Use `GROUP BY` on the combined list to count the number of occurrences (friends) for each user.

4. **Find the User with the Most Friends:**
   - Order the results by the count in descending order and limit the result to one row.

---

### Query:

```sql
WITH a AS (
    SELECT requester_id AS id FROM RequestAccepted
    UNION ALL
    SELECT accepter_id AS id FROM RequestAccepted
)

SELECT id, COUNT(id) AS num
FROM a 
GROUP BY id
ORDER BY COUNT(id) DESC
LIMIT 1;
```

---

### Explanation of the Query:

1. **Combine All Friendships:**
   - The `WITH a` clause creates a temporary table containing all `requester_id` and `accepter_id` values in a single column named `id`.
   - `UNION ALL` ensures that duplicates are not removed, as multiple friendships should count multiple times.

2. **Count Friends:**
   - The main query groups the data by `id` and counts the occurrences of each `id`, representing the number of friends each user has.

3. **Find the Maximum:**
   - The `ORDER BY COUNT(id) DESC` sorts the users by their friend count in descending order.
   - `LIMIT 1` retrieves the top user with the highest number of friends.

---

### Output:

For the given input, the output will be:

```
+----+-----+
| id | num |
+----+-----+
| 3  | 3   |
+----+-----+
