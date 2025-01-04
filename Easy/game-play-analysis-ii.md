# Game Play Analysis II
### Problem

#### Table: Activity

| Column Name  | Type    |
|--------------|---------|
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |

- `(player_id, event_date)` is the primary key (combination of columns with unique values) of this table.
- This table shows the activity of players of some games.
- Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out using a device on a specific day.

#### Task
Write a solution to report the `device_id` that is first logged in for each `player_id`.

Return the result table in any order.

#### Example

**Input:**

`Activity` table:

| player_id | device_id | event_date | games_played |
|-----------|-----------|------------|--------------|
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |

**Output:**

| player_id | device_id |
|-----------|-----------|
| 1         | 2         |
| 2         | 3         |
| 3         | 1         |

**Explanation:**
- For `player_id = 1`, the first login date is `2016-03-01`, and the corresponding `device_id` is `2`.
- For `player_id = 2`, the first login date is `2017-06-25`, and the corresponding `device_id` is `3`.
- For `player_id = 3`, the first login date is `2016-03-02`, and the corresponding `device_id` is `1`.

---

### Thought Process

To solve this problem, the goal is to find the device used for the first login for each player. Here’s how I approached it step by step:

1. **Understand the Requirement:**
   - Identify the earliest `event_date` (first login) for each `player_id`.
   - Find the corresponding `device_id` for that login date.

2. **Find the Earliest Login Date:**
   - Use the `MIN()` function with `GROUP BY player_id` to get the earliest `event_date` for each player.

3. **Match the Earliest Date to Device:**
   - Use a subquery to identify the rows where the combination of `player_id` and `event_date` matches the earliest login date.

4. **Select Required Columns:**
   - Return the `player_id` and the corresponding `device_id`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT device_id, player_id 
FROM Activity 
WHERE (player_id, event_date) IN (
  SELECT player_id, MIN(event_date) 
  FROM Activity 
  GROUP BY player_id
);
```

---

### Solution Explanation

- **Subquery:**
  - `SELECT player_id, MIN(event_date) FROM Activity GROUP BY player_id` finds the earliest `event_date` for each `player_id`.
  - The subquery returns a list of `(player_id, event_date)` pairs representing the first login date for each player.

- **Main Query:**
  - The `WHERE` clause checks if each row in the `Activity` table matches a `(player_id, event_date)` pair from the subquery.
  - Only rows with the earliest login dates are selected.

- **Select Statement:**
  - Returns the `player_id` and corresponding `device_id` for the first login.

---

### Key Takeaways

- **Subqueries in SQL:** Useful for filtering data based on aggregated results like `MIN()`.
- **IN Clause:** Efficient for matching rows against a list of values.
- **Primary Keys:** Leveraging unique constraints like `(player_id, event_date)` ensures accurate results.

---

### Related Topics
- Subqueries
- Aggregate Functions
- GROUP BY Clause
- Filtering with WHERE and IN
