### Friends With No Mutual Friends

We are given the following table:

#### Table: Friends
| Column Name | Type |
|-------------|------|
| user_id1    | int  |
| user_id2    | int  |

- `(user_id1, user_id2)` is the primary key for this table.
- Each row indicates that `user_id1` and `user_id2` are friends with each other.

#### Task
Write a query to find all pairs of users who are friends with each other and have no mutual friends. The result table should be ordered by `user_id1` and `user_id2` in ascending order.

---

### Example Input/Output

##### Input:
**Friends Table:**
| user_id1 | user_id2 |
|----------|----------|
| 1        | 2        |
| 2        | 3        |
| 2        | 4        |
| 1        | 5        |
| 6        | 7        |
| 3        | 4        |
| 2        | 5        |
| 8        | 9        |

##### Output:
| user_id1 | user_id2 |
|----------|----------|
| 6        | 7        |
| 8        | 9        |

##### Explanation:
- **Excluded Pairs:**
  - Users `1` and `2` share a mutual friend with `5`.
  - Users `2` and `3` share a mutual friend with `4`.
  - Users `2` and `4` share a mutual friend with `3`.
  - Users `1` and `5` share a mutual friend with `2`.
  - Users `3` and `4` share a mutual friend with `2`.
  - Users `2` and `5` share a mutual friend with `1`.
- **Included Pairs:**
  - Users `6` and `7` have no mutual friends.
  - Users `8` and `9` have no mutual friends.

---

### Thought Process
1. **Understand the Problem:**
   - A "mutual friend" is defined as a user connected to both `user_id1` and `user_id2`.
   - To find pairs with no mutual friends, we need to check if there exists no user connected to both users in each pair.

2. **Normalize the Friends Table:**
   - Use a `UNION` query to make the friendship relationship symmetrical (e.g., `(1, 2)` and `(2, 1)`).

3. **Identify Mutual Friendships:**
   - Self-join the normalized table to find users who share a mutual friend.

4. **Exclude Pairs with Mutual Friends:**
   - Filter out pairs that have any common mutual friends.

5. **Order the Result:**
   - Ensure the output is sorted by `user_id1` and `user_id2` in ascending order.

---

### Query
```sql
WITH CTE AS (
    SELECT user_id1, user_id2 
    FROM friends
    UNION
    SELECT user_id2, user_id1 
    FROM friends
)

SELECT *
FROM friends
WHERE (user_id1, user_id2) NOT IN (
    SELECT a.user_id1, b.user_id1
    FROM cte a
    JOIN cte b 
    ON a.user_id2 = b.user_id2
)
ORDER BY 1, 2;
```

---

### Explanation of the Query
1. **CTE Step:**
   - Create a normalized table (`CTE`) that includes both `(user_id1, user_id2)` and `(user_id2, user_id1)` to ensure symmetry.

2. **Identify Mutual Friendships:**
   - Perform a self-join on `CTE` to identify mutual friends, where `a.user_id2 = b.user_id2`.

3. **Exclude Pairs with Mutual Friends:**
   - Use a `NOT IN` condition to exclude pairs from the `friends` table that have mutual friends.

4. **Ordering:**
   - The result is sorted by `user_id1` and `user_id2` in ascending order.

---

### Key Takeaways
- **Normalization:** Symmetrical relationships should be normalized for ease of processing.
- **Self-Join:** Self-joins are powerful for identifying relationships like mutual friends.
- **Exclusion Logic:** The `NOT IN` clause is used effectively to filter out unwanted rows.
- **Sorting:** Ensure the final result meets the output requirements for ordering.
