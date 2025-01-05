
### League Statistics

#### Tables:

**Teams**

| Column Name | Type    |
|-------------|---------|
| team_id     | int     |
| team_name   | varchar |

- `team_id` is the unique identifier for each team.
- `team_name` contains the name of the team.

**Matches**

| Column Name     | Type |
|-----------------|------|
| home_team_id    | int  |
| away_team_id    | int  |
| home_team_goals | int  |
| away_team_goals | int  |

- `(home_team_id, away_team_id)` is the primary key.
- `home_team_goals` and `away_team_goals` record the goals scored by the home and away teams respectively.

#### Task:
Write a query to generate league statistics. The output should include:
- `team_name`: Name of the team.
- `matches_played`: Total matches played by the team.
- `points`: Total points accumulated based on match results.
  - Win: 3 points.
  - Draw: 1 point.
  - Loss: 0 points.
- `goal_for`: Total goals scored by the team.
- `goal_against`: Total goals conceded by the team.
- `goal_diff`: Difference between goals scored and goals conceded (`goal_for - goal_against`).

#### Output Order:
- By `points` in descending order.
- By `goal_diff` in descending order if points are equal.
- By `team_name` in lexicographical order if both points and goal difference are equal.

---

### Thought Process:

1. **Match Data Transformation:**
   - Combine `home_team` and `away_team` match data into a single table using `UNION ALL`.
   - For each match, calculate goals scored and conceded based on home or away status.

2. **Points Calculation:**
   - For each match, assign points:
     - 3 points for a win.
     - 1 point for a draw.
     - 0 points for a loss.

3. **Aggregate Statistics:**
   - Group data by team and calculate:
     - Total matches played.
     - Total points.
     - Total goals scored and conceded.
     - Goal difference.

4. **Join Team Names:**
   - Join the aggregated statistics with the `Teams` table to retrieve team names.

5. **Sorting:**
   - Order results by `points` (desc), `goal_diff` (desc), and `team_name` (asc).

---

### Query:

```sql
WITH cte AS (
    SELECT 
        home_team_id AS home_team, 
        away_team_id AS away_team, 
        home_team_goals AS home_goals,
        away_team_goals AS away_goals
    FROM matches
    UNION ALL
    SELECT 
        away_team_id AS home_team,
        home_team_id AS away_team, 
        away_team_goals AS home_goals,
        home_team_goals AS away_goals
    FROM matches
)

SELECT 
    t.team_name, 
    COUNT(c.home_team) AS matches_played,
    SUM(
        CASE 
            WHEN c.home_goals > c.away_goals THEN 3 
            WHEN c.home_goals = c.away_goals THEN 1 
            ELSE 0 
        END
    ) AS points,
    SUM(c.home_goals) AS goal_for, 
    SUM(c.away_goals) AS goal_against,
    SUM(c.home_goals) - SUM(c.away_goals) AS goal_diff
FROM cte c
JOIN teams t
    ON c.home_team = t.team_id
GROUP BY c.home_team, t.team_name
ORDER BY points DESC, goal_diff DESC, t.team_name;
