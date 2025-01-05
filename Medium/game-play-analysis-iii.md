### Game Play Analysis III

**Table: Activity**

```text
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```

- `(player_id, event_date)` is the primary key (column with unique values) of this table.
- This table shows the activity of players of some games.
- Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.

Write a solution to report for each player and date, how many games played so far by the player. That is, the total number of games played by the player until that date. Check the example for clarity.

Return the result table in any order.

### Example:

#### Input:

**Activity table:**

```text
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 1         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
```

#### Output:

```text
+-----------+------------+---------------------+
| player_id | event_date | games_played_so_far |
+-----------+------------+---------------------+
| 1         | 2016-03-01 | 5                   |
| 1         | 2016-05-02 | 11                  |
| 1         | 2017-06-25 | 12                  |
| 3         | 2016-03-02 | 0                   |
| 3         | 2018-07-03 | 5                   |
+-----------+------------+---------------------+
```

#### Explanation:

- For the player with `player_id = 1`:
  - On `2016-03-01`, they played `5` games.
  - By `2016-05-02`, they had played `5 + 6 = 11` games.
  - By `2017-06-25`, they had played `5 + 6 + 1 = 12` games.
- For the player with `player_id = 3`:
  - On `2016-03-02`, they played `0` games.
  - By `2018-07-03`, they had played `0 + 5 = 5` games.
- The result is returned in descending order of `event_date` for each `player_id`.

---

### Thought Process:

1. **Understand the Requirement:**
   - We need to calculate a running total of `games_played` for each player (`player_id`) across their `event_date`.
   - The running total should reset for each player and be ordered by `event_date`.

2. **Break Down the Problem:**
   - Use a `SUM()` window function to calculate the cumulative `games_played` for each `player_id`.
   - Partition the calculation by `player_id` to handle each player's records independently.
   - Order the records by `event_date` within each partition to calculate the running total correctly.

3. **Sorting the Output:**
   - The output should be sorted by `player_id` and `event_date` in descending order.

---

### Query:

```sql
SELECT 
    player_id, 
    event_date, 
    SUM(games_played) OVER (
        PARTITION BY player_id 
        ORDER BY event_date
    ) AS games_played_so_far
FROM 
    Activity
ORDER BY 
    player_id, 
    event_date DESC;
```

---

### Explanation of the Query:

1. **Window Function:**
   - `SUM(games_played) OVER (PARTITION BY player_id ORDER BY event_date)`:
     - This calculates a running total of `games_played` for each `player_id` in the order of `event_date`.
2. **Output Columns:**
   - `player_id`: Identifies the player.
   - `event_date`: Indicates the date of the activity.
   - `games_played_so_far`: Shows the cumulative total of games played up to and including the `event_date`.
3. **Sorting:**
   - The result is sorted by `player_id` and `event_date` in descending order, as specified.

---

### Why This Query Works:
- The `PARTITION BY` ensures that each player's data is calculated independently.
- The `ORDER BY` within the window function ensures that the cumulative total is calculated in the correct chronological order.
- The final `ORDER BY` ensures the result meets the required sorting order.

---
