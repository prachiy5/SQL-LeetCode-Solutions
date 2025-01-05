# Game Play Analysis V

You are given a table:

### Activity Table
| Column Name  | Type    |
|--------------|---------|
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |

- `(player_id, event_date)` is the primary key (combination of columns with unique values) for this table.
- Each row in this table represents a player's activity on a specific day, including their `player_id`, the `device_id` they used, the `event_date`, and the number of `games_played`.

### Task

The **install date** of a player is defined as their first login day, i.e., the earliest `event_date` for each `player_id`.

**Day One Retention** for a specific install date `x` is calculated as:
- The number of players whose install date is `x` and who logged back in on the day right after `x`,
- Divided by the number of players whose install date is `x`,
- Rounded to 2 decimal places.

### Requirements

For each install date, report the following:
1. **install_dt**: The install date.
2. **installs**: The number of players who installed the game on that day.
3. **Day1_retention**: The day one retention rate for that day.

### Result Table
Return the result table in any order.

---

### Input Example
#### Activity Table:
| player_id | device_id | event_date | games_played |
|-----------|-----------|------------|--------------|
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-01 | 0            |
| 3         | 4         | 2016-07-03 | 5            |

#### Output Example:
| install_dt | installs | Day1_retention |
|------------|----------|----------------|
| 2016-03-01 | 2        | 0.50           |
| 2017-06-25 | 1        | 0.00           |

---

### Explanation
1. **Install Date: 2016-03-01**
   - Players: 1, 3.
   - Player 1 logged back in on `2016-03-02`, but Player 3 did not.
   - **Day One Retention** = `1 / 2 = 0.50`.

2. **Install Date: 2017-06-25**
   - Player: 2.
   - Player 2 did not log back in on `2017-06-26`.
   - **Day One Retention** = `0 / 1 = 0.00`.

---

### Thought Process
1. **Identify Install Dates**:
   - Find the first `event_date` for each player (`min(event_date)`).

2. **Count Installs**:
   - Count the number of players whose install date matches each `event_date`.

3. **Count Day One Logins**:
   - Identify players who logged back in on the day immediately following their install date (`install_date + 1`).

4. **Calculate Day One Retention**:
   - Divide the number of players with day one logins by the total number of installs for each `event_date`.
   - Round the result to 2 decimal places.

5. **Join and Aggregate Data**:
   - Use a `LEFT JOIN` to calculate the required metrics.

6. **Output Results**:
   - Display the install date, total installs, and day one retention rate.

---

### Query
```sql
SELECT 
    A.event_date AS install_dt, 
    COUNT(A.player_id) AS installs, 
    ROUND(COUNT(B.player_id) / COUNT(A.player_id), 2) AS Day1_retention
FROM 
    (SELECT MIN(event_date) AS event_date, player_id FROM Activity GROUP BY player_id) AS A
LEFT JOIN 
    Activity B
ON 
    A.player_id = B.player_id AND B.event_date = A.event_date + 1
GROUP BY 
    A.event_date;
