# Premier League Table Ranking III

You are given a table `SeasonStats` with the following structure:

| Column Name      | Type    |
|------------------|---------|
| season_id        | int     |
| team_id          | int     |
| team_name        | varchar |
| matches_played   | int     |
| wins             | int     |
| draws            | int     |
| losses           | int     |
| goals_for        | int     |
| goals_against    | int     |

- `(season_id, team_id)` is the unique key for this table.
- This table contains season ID, team ID, team name, matches played, wins, draws, losses, goals scored (`goals_for`), and goals conceded (`goals_against`) for each team in each season.

### Task

Calculate the points, goal difference, and rank for each team in each season. The ranking should be determined as follows:

1. Teams are ranked by their total points (highest to lowest).
2. If points are tied, teams are ranked by their goal difference (highest to lowest).
3. If goal difference is also tied, teams are ranked alphabetically by team name.

#### Points Calculation:
- 3 points for a win
- 1 point for a draw
- 0 points for a loss

#### Goal Difference Calculation:
- `goals_for - goals_against`

Return the result table ordered by `season_id` in ascending order, then by `rank` in ascending order, and finally by `team_name` in ascending order.

### Input Example
#### SeasonStats Table:

| season_id  | team_id | team_name         | matches_played | wins | draws | losses | goals_for | goals_against |
|------------|---------|-------------------|----------------|------|-------|--------|-----------|---------------|
| 2021       | 1       | Manchester City   | 38             | 29   | 6     | 3      | 99        | 26            |
| 2021       | 2       | Liverpool         | 38             | 28   | 8     | 2      | 94        | 26            |
| 2021       | 3       | Chelsea           | 38             | 21   | 11    | 6      | 76        | 33            |
| 2021       | 4       | Tottenham         | 38             | 22   | 5     | 11     | 69        | 40            |
| 2021       | 5       | Arsenal           | 38             | 22   | 3     | 13     | 61        | 48            |
| 2022       | 1       | Manchester City   | 38             | 28   | 5     | 5      | 94        | 33            |
| 2022       | 2       | Arsenal           | 38             | 26   | 6     | 6      | 88        | 43            |
| 2022       | 3       | Manchester United | 38             | 23   | 6     | 9      | 58        | 43            |
| 2022       | 4       | Newcastle         | 38             | 19   | 14    | 5      | 68        | 33            |
| 2022       | 5       | Liverpool         | 38             | 19   | 10    | 9      | 75        | 47            |

### Output Example
| season_id  | team_id | team_name         | points | goal_difference | position |
|------------|---------|-------------------|--------|-----------------|----------|
| 2021       | 1       | Manchester City   | 93     | 73              | 1        |
| 2021       | 2       | Liverpool         | 92     | 68              | 2        |
| 2021       | 3       | Chelsea           | 74     | 43              | 3        |
| 2021       | 4       | Tottenham         | 71     | 29              | 4        |
| 2021       | 5       | Arsenal           | 69     | 13              | 5        |
| 2022       | 1       | Manchester City   | 89     | 61              | 1        |
| 2022       | 2       | Arsenal           | 84     | 45              | 2        |
| 2022       | 3       | Manchester United | 75     | 15              | 3        |
| 2022       | 4       | Newcastle         | 71     | 35              | 4        |
| 2022       | 5       | Liverpool         | 67     | 28              | 5        |

### Thought Process
1. **Calculate Points**:
   - Use the formula: `wins * 3 + draws` to calculate total points for each team.

2. **Calculate Goal Difference**:
   - Use the formula: `goals_for - goals_against`.

3. **Rank Teams**:
   - Use `DENSE_RANK()` with the following order:
     - `points` (descending).
     - `goal_difference` (descending).
     - `team_name` (ascending).

4. **Sort Results**:
   - Order the final result by `season_id`, then `rank`, and then `team_name`.

### Query
```sql
WITH cte AS (
    SELECT 
        season_id,
        team_id,
        team_name,
        wins * 3 + draws AS points,
        goals_for - goals_against AS goal_difference
    FROM 
        seasonstats
)

SELECT 
    *,
    DENSE_RANK() OVER (
        PARTITION BY season_id 
        ORDER BY points DESC, goal_difference DESC, team_name
    ) AS position
FROM 
    cte
ORDER BY 
    season_id, position, team_name;
