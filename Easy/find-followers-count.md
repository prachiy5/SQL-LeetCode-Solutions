# Find Followers Count
### Problem

#### Table: Followers

| Column Name | Type |
|-------------|------|
| user_id     | int  |
| follower_id | int  |

- `(user_id, follower_id)` is the primary key.
- This table records followers in a social media app where `follower_id` follows `user_id`.

#### Task

For each user, count the number of followers and return the result sorted by `user_id` in ascending order.

---

### Example

#### Input

**Followers Table**:

| user_id | follower_id |
|---------|-------------|
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |

#### Output

| user_id | followers_count |
|---------|------------------|
| 0       | 1                |
| 1       | 1                |
| 2       | 2                |

#### Explanation

- User `0` has 1 follower: `{1}`.
- User `1` has 1 follower: `{0}`.
- User `2` has 2 followers: `{0, 1}`.

---

### Thought Process

1. **Understand the Structure**:
   - The `Followers` table links a `follower_id` to a `user_id`.
   - Counting the `follower_id` for each `user_id` gives the number of followers.

2. **Group and Aggregate**:
   - Use `GROUP BY user_id` to group rows by `user_id`.
   - Use `COUNT(follower_id)` to count the followers for each `user_id`.

3. **Sort Results**:
   - Use `ORDER BY user_id` to ensure the output is sorted in ascending order by `user_id`.

---

### Solution

```sql
SELECT 
    user_id, 
    COUNT(follower_id) AS followers_count
FROM 
    followers
GROUP BY 
    user_id
ORDER BY 
    user_id;
```

---

### Solution Explanation

1. **SELECT Clause**:
   - `user_id`: Identifies the user.
   - `COUNT(follower_id)`: Counts the number of rows (followers) for each `user_id`.

2. **GROUP BY Clause**:
   - Groups all rows with the same `user_id` together for aggregation.

3. **ORDER BY Clause**:
   - Ensures the result is sorted by `user_id` in ascending order.

---

### Key Takeaways

- Use `GROUP BY` to aggregate data for specific columns.
- Use `COUNT(column)` to count non-null values in the specified column.
- Sorting results ensures clarity and meets task requirements.

---

### Related Topics

- SQL Aggregation Functions
- GROUP BY Clause
- Sorting with ORDER BY
