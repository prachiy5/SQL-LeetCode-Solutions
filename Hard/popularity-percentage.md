# Popularity Percentage

## Table: Friends
| Column Name | Type |
|-------------|------|
| user1       | int  |
| user2       | int  |

- `(user1, user2)` is the primary key (combination of unique values) of this table.
- Each row contains information about a friendship where `user1` and `user2` are friends.

## Task
Write a solution to find the **popularity percentage** for each user on Meta/Facebook. The popularity percentage is defined as:

\[
\text{Popularity Percentage} = \left(\frac{\text{Total Friends of the User}}{\text{Total Users on the Platform}}\right) \times 100
\]

- The result should be rounded to **two decimal places**.
- Return the result table ordered by `user1` in ascending order.

---

# Example

### Input

#### Friends Table:
| user1 | user2 |
|-------|-------|
| 2     | 1     |
| 1     | 3     |
| 4     | 1     |
| 1     | 5     |
| 1     | 6     |
| 2     | 6     |
| 7     | 2     |
| 8     | 3     |
| 3     | 9     |

### Output

| user1 | percentage_popularity |
|-------|-----------------------|
| 1     | 55.56                 |
| 2     | 33.33                 |
| 3     | 33.33                 |
| 4     | 11.11                 |
| 5     | 11.11                 |
| 6     | 22.22                 |
| 7     | 11.11                 |
| 8     | 11.11                 |
| 9     | 11.11                 |

### Explanation
1. There are a total of 9 users on the platform.
2. The popularity percentage for each user is calculated as:
   - User 1 has 5 friends: \((5/9) \times 100 = 55.56\)
   - User 2 has 3 friends: \((3/9) \times 100 = 33.33\)
   - User 3 has 3 friends: \((3/9) \times 100 = 33.33\)
   - User 4 has 1 friend: \((1/9) \times 100 = 11.11\)
   - User 5 has 1 friend: \((1/9) \times 100 = 11.11\)
   - User 6 has 2 friends: \((2/9) \times 100 = 22.22\)
   - User 7 has 1 friend: \((1/9) \times 100 = 11.11\)
   - User 8 has 1 friend: \((1/9) \times 100 = 11.11\)
   - User 9 has 1 friend: \((1/9) \times 100 = 11.11\)

---

# Thought Process

1. **Union Friendships**:
   - Friendships are bi-directional (if A is friends with B, then B is also friends with A).
   - Use a `UNION` query to list both directions of the friendship (i.e., `user1 -> user2` and `user2 -> user1`).

2. **Get Total Users**:
   - Use `DISTINCT` on `user1` and `user2` to find all unique users on the platform.

3. **Count Friends for Each User**:
   - Group the friendships by `user1` and count the number of friends.

4. **Calculate Popularity Percentage**:
   - Divide the total number of friends by the total number of users and multiply by 100.
   - Round the result to two decimal places.

5. **Return Results**:
   - Include `user1` and their popularity percentage.
   - Order the results by `user1` in ascending order.

---

# Solution

```sql
WITH cte AS (
    SELECT user1, user2
    FROM Friends
    UNION
    SELECT user2, user1
    FROM Friends
),

all_users AS (
    SELECT DISTINCT user1
    FROM Friends
    UNION
    SELECT DISTINCT user2
    FROM Friends
)

SELECT 
    user1,
    ROUND(COUNT(*) * 100 / (SELECT COUNT(*) FROM all_users), 2) AS percentage_popularity
FROM cte
GROUP BY user1
ORDER BY user1;
```

---

# Solution Explanation

1. **CTE (`cte`)**:
   - Use a `UNION` to include both directions of the friendship.

2. **CTE (`all_users`)**:
   - Combine `user1` and `user2` to find all unique users on the platform.

3. **Main Query**:
   - Count the number of friends (`COUNT(*)`) for each user.
   - Calculate the percentage popularity by dividing the number of friends by the total number of users and multiplying by 100.
   - Use `ROUND` to round the result to two decimal places.

4. **Ordering**:
   - Return the result ordered by `user1` in ascending order.

---

# Key Takeaways
- Use `UNION` to handle bi-directional relationships in a table.
- Calculate percentages using aggregation and subqueries for total counts.
- Round numeric results to specific decimal places using `ROUND`.

---

# Related Topics
- Common Table Expressions (CTEs)
- Aggregation (`COUNT`, `ROUND`)
- Set Operations (`UNION`)
