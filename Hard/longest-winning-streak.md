#### Longest Winning Streak

We are given a table:

**Table: Matches**

| Column Name | Type |
|-------------|------|
| player_id   | int  |
| match_day   | date |
| result      | enum |

- **player_id** and **match_day** together form the primary key.
- Each row contains the ID of a player, the day of the match, and the result of that match.
- The **result** column is of ENUM type with values ('Win', 'Draw', 'Lose').

The task is to calculate the **longest winning streak** for each player. A winning streak is defined as a series of consecutive wins uninterrupted by draws or losses.

The result table should contain:

| Column Name      | Description                    |
|------------------|--------------------------------|
| player_id        | ID of the player.              |
| longest_streak   | Longest streak of consecutive wins for the player. |

The result should return one row per player, and the rows can be in any order.

---

**Example Input and Output**

**Input:**

| player_id | match_day  | result |
|-----------|------------|--------|
| 1         | 2022-01-17 | Win    |
| 1         | 2022-01-18 | Win    |
| 1         | 2022-01-25 | Win    |
| 1         | 2022-01-31 | Draw   |
| 1         | 2022-02-08 | Win    |
| 2         | 2022-02-06 | Lose   |
| 2         | 2022-02-08 | Lose   |
| 3         | 2022-03-30 | Win    |

**Output:**

| player_id | longest_streak |
|-----------|----------------|
| 1         | 3              |
| 2         | 0              |
| 3         | 1              |

**Explanation:**
- For player 1: The longest winning streak is 3 matches (2022-01-17 to 2022-01-25).
- For player 2: No winning streak as all matches were losses.
- For player 3: The longest winning streak is 1 match (2022-03-30).

---

### Thought Process

1. Identify winning matches for each player.
2. Assign a sequential number to each match for a player based on the match date.
3. Identify streaks by grouping matches where the difference between the sequential number and the date sequence is constant.
4. Calculate the length of each streak.
5. Retrieve the maximum streak length for each player.

---

### SQL Query

```sql
WITH cte AS (
    SELECT *,
        ROW_NUMBER() OVER (PARTITION BY player_id ORDER BY match_day) ck1,
        ROW_NUMBER() OVER (PARTITION BY player_id, result ORDER BY match_day) ck2
    FROM Matches
)

SELECT m.player_id, IFNULL(MAX(streak), 0) longest_streak
FROM 
(SELECT DISTINCT player_id FROM Matches) m 
LEFT JOIN
(SELECT player_id, 
    COUNT(match_day) streak
FROM cte
WHERE result = 'Win'
GROUP BY 1, ck1-ck2) t
ON m.player_id = t.player_id
GROUP BY 1;
```

---

### Explanation of the Query

1. **`cte` Common Table Expression:**
   - Assigns two row numbers:
     - `ck1`: Sequential row number for each player ordered by match_day.
     - `ck2`: Sequential row number for each player and result type ordered by match_day.
   - The difference `ck1 - ck2` helps identify consecutive streaks of the same result.

2. **Filtering for Wins:**
   - From the CTE, select only rows where `result = 'Win'`.
   - Group by `player_id` and the difference (`ck1 - ck2`) to calculate the length of each streak.

3. **Calculating Longest Streak:**
   - Use `MAX(streak)` to get the longest streak for each player.
   - Use `LEFT JOIN` to ensure players with no wins are included with a streak of 0.

4. **Final Output:**
   - Return the player ID and their longest winning streak.

---

### Follow-Up: Longest Streak Without Losing

To calculate the longest streak without losing (i.e., 'Win' or 'Draw'):

- Update the filter condition in the second step to `WHERE result IN ('Win', 'Draw')`.
- This will group streaks of both 'Win' and 'Draw' results together.
- The rest of the logic remains the same.

---

### Key Points
- Using `ROW_NUMBER()` and difference grouping is a powerful technique to identify consecutive streaks.
- The `LEFT JOIN` ensures all players are included in the result, even those with no wins.
- The query is flexible and can be adjusted for other streak definitions.
