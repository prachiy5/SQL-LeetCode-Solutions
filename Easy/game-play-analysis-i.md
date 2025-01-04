# Game Play Analysis 1

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
- Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on a specific day using a device.

#### Task
Write a solution to find the first login date for each player.

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

| player_id | first_login |
|-----------|-------------|
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |

**Explanation:**
- For `player_id = 1`, the first login date is `2016-03-01`.
- For `player_id = 2`, the first login date is `2017-06-25`.
- For `player_id = 3`, the first login date is `2016-03-02`.

---

### Thought Process

To solve this problem, the goal is to find the earliest `event_date` (first login date) for each player. Here’s how I approached it step by step:

1. **Understand the Table:**
   - Each row represents a player's activity on a specific date.
   - The `event_date` column tells us the date of the activity.
   - We need the earliest `event_date` for each `player_id`.

2. **Group Players:**
   - Use the `GROUP BY` clause to group rows by `player_id`.

3. **Find Minimum Date:**
   - Use the `MIN()` function to find the earliest `event_date` for each group of `player_id`.

4. **Select Relevant Columns:**
   - Return the `player_id` and the earliest `event_date` as `first_login`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT player_id, MIN(event_date) AS first_login
FROM Activity
GROUP BY player_id;
```

---

### Solution Explanation

- **GROUP BY:**
  - Groups all rows by `player_id`.
  - Ensures that all activity for each player is considered as a single group.

- **MIN Function:**
  - Finds the smallest (earliest) `event_date` for each group.

- **SELECT Statement:**
  - Returns the `player_id` and the calculated earliest `event_date` as `first_login`.

---

### Key Takeaways

- **Grouping:** Use `GROUP BY` to aggregate data based on a specific column.
- **Aggregate Functions:** The `MIN()` function is perfect for finding the smallest value in a group.
- **Column Aliases:** Use aliases like `first_login` to make the output more readable.

---

### Related Topics
- Aggregation in SQL
- GROUP BY Clause
- Using Aggregate Functions like MIN, MAX, COUNT
