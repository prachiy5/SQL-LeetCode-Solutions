### Team Scores in Football Tournament
We are given two tables:

#### Table: `Teams`

| Column Name | Type    |
|-------------|---------|
| team_id     | int     |
| team_name   | varchar |

- `team_id` is the primary key and contains unique values.
- Each row represents a single football team.

#### Table: `Matches`

| Column Name | Type |
|-------------|------|
| match_id    | int  |
| host_team   | int  |
| guest_team  | int  |
| host_goals  | int  |
| guest_goals | int  |

- `match_id` is the primary key and contains unique values.
- Each row records a finished match between two different teams:
  - `host_team` and `guest_team` represent the IDs of the teams involved in the match (referencing `team_id` in the `Teams` table).
  - `host_goals` and `guest_goals` represent the goals scored by the `host_team` and `guest_team`, respectively.

### Scoring Rules
- A team receives **3 points** if they win a match (scored more goals than the opponent).
- A team receives **1 point** if the match ends in a draw (both teams scored the same number of goals).
- A team receives **0 points** if they lose a match (scored fewer goals than the opponent).

### Task
Write a SQL query to calculate the total points (`num_points`) for each team after all matches. Return the result with the following columns:
- `team_id`
- `team_name`
- `num_points`

The result should be ordered by `num_points` in **descending order**. In case of a tie, order by `team_id` in **ascending order**.

### Example
#### Input:
**Teams table:**

| team_id | team_name   |
|---------|-------------|
| 10      | Leetcode FC |
| 20      | NewYork FC  |
| 30      | Atlanta FC  |
| 40      | Chicago FC  |
| 50      | Toronto FC  |

**Matches table:**

| match_id | host_team | guest_team | host_goals | guest_goals |
|----------|-----------|------------|------------|-------------|
| 1        | 10        | 20         | 3          | 0           |
| 2        | 30        | 10         | 2          | 2           |
| 3        | 10        | 50         | 5          | 1           |
| 4        | 20        | 30         | 1          | 0           |
| 5        | 50        | 30         | 1          | 0           |

#### Output:

| team_id | team_name   | num_points |
|---------|-------------|------------|
| 10      | Leetcode FC | 7          |
| 20      | NewYork FC  | 3          |
| 50      | Toronto FC  | 3          |
| 30      | Atlanta FC  | 1          |
| 40      | Chicago FC  | 0          |

#### Explanation:
1. **Leetcode FC (ID 10):**
   - Match 1: Win (3 points)
   - Match 2: Draw (1 point)
   - Match 3: Win (3 points)
   - Total: 7 points
2. **NewYork FC (ID 20):**
   - Match 1: Loss (0 points)
   - Match 4: Win (3 points)
   - Total: 3 points
3. **Toronto FC (ID 50):**
   - Match 3: Loss (0 points)
   - Match 5: Win (3 points)
   - Total: 3 points
4. **Atlanta FC (ID 30):**
   - Match 2: Draw (1 point)
   - Match 4: Loss (0 points)
   - Match 5: Loss (0 points)
   - Total: 1 point
5. **Chicago FC (ID 40):**
   - No matches played.
   - Total: 0 points

---

### Thought Process
1. **Combine Tables**:
   - Use a `LEFT JOIN` between the `Teams` table and `Matches` table to associate each team with matches they participated in, either as the host or guest.
2. **Calculate Points**:
   - Use a `CASE` statement to assign points based on the match results:
     - **3 points** if the team wins (more goals than the opponent).
     - **1 point** if the match ends in a draw.
     - **0 points** otherwise.
3. **Group and Aggregate**:
   - Group by `team_id` and calculate the total points for each team.
4. **Order the Results**:
   - Sort by `num_points` in descending order.
   - In case of a tie, sort by `team_id` in ascending order.

---

### SQL Query
```sql
WITH cte AS (
    SELECT * 
    FROM teams
    LEFT JOIN matches
    ON team_id = host_team OR team_id = guest_team
)

SELECT 
    team_id, 
    team_name,
    SUM(
        CASE 
            WHEN team_id = host_team AND host_goals > guest_goals THEN 3
            WHEN team_id = guest_team AND guest_goals > host_goals THEN 3
            WHEN team_id = host_team AND host_goals = guest_goals THEN 1
            WHEN team_id = guest_team AND guest_goals = host_goals THEN 1
            ELSE 0
        END
    ) AS num_points
FROM cte
GROUP BY team_id
ORDER BY num_points DESC, team_id ASC;
