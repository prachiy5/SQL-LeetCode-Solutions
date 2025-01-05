# Premier League Table Ranking II

You are given a table `TeamStats` with the following structure:

| Column Name      | Type    |
|------------------|---------|
| team_id          | int     |
| team_name        | varchar |
| matches_played   | int     |
| wins             | int     |
| draws            | int     |
| losses           | int     |

- `team_id` is the unique identifier for this table.
- This table contains the team ID, team name, matches played, wins, draws, and losses.

### Task

Calculate the following for each team in the league:
1. **Points**: Calculated as:
   - 3 points for a win.
   - 1 point for a draw.
   - 0 points for a loss.
2. **Position**: Teams with the same points must share the same position.
3. **Tier**: Divide the league into 3 tiers based on points:
   - Tier 1: Top 33% of teams.
   - Tier 2: Middle 33% of teams.
   - Tier 3: Bottom 34% of teams.
   - For ties at tier boundaries, place tied teams in the higher tier.

### Output
The result table should be ordered by points in descending order and then by team_name in ascending order.

### Input Example
#### TeamStats Table:

| team_id | team_name         | matches_played | wins | draws | losses |
|---------|-------------------|----------------|------|-------|--------|
| 1       | Chelsea           | 22             | 13   | 2     | 7      |
| 2       | Nottingham Forest | 27             | 6    | 6     | 15     |
| 3       | Liverpool         | 17             | 1    | 8     | 8      |
| 4       | Aston Villa       | 20             | 1    | 6     | 13     |
| 5       | Fulham            | 31             | 18   | 1     | 12     |
| 6       | Burnley           | 26             | 6    | 9     | 11     |
| 7       | Newcastle United  | 33             | 11   | 10    | 12     |
| 8       | Sheffield United  | 20             | 18   | 2     | 0      |
| 9       | Luton Town        | 5              | 4    | 0     | 1      |
| 10      | Everton           | 14             | 2    | 6     | 6      |

### Output Example

| team_name         | points | position | tier    |
|-------------------|--------|----------|---------|
| Sheffield United  | 56     | 1        | Tier 1  |
| Fulham            | 55     | 2        | Tier 1  |
| Newcastle United  | 43     | 3        | Tier 1  |
| Chelsea           | 41     | 4        | Tier 1  |
| Burnley           | 27     | 5        | Tier 2  |
| Nottingham Forest | 24     | 6        | Tier 2  |
| Everton           | 12     | 7        | Tier 2  |
| Luton Town        | 12     | 7        | Tier 2  |
| Liverpool         | 11     | 9        | Tier 3  |
| Aston Villa       | 9      | 10       | Tier 3  |

### Explanation

- **Points Calculation**:
  - Sheffield United has 56 points: (18 wins * 3 points) + (2 draws * 1 point).
  - Fulham has 55 points: (18 wins * 3 points) + (1 draw * 1 point).
  - Newcastle United has 43 points: (11 wins * 3 points) + (10 draws * 1 point).
  - Chelsea has 41 points: (13 wins * 3 points) + (2 draws * 1 point).
  - Other teams' points are calculated similarly.

- **Position Assignment**:
  - Sheffield United is in position 1.
  - Fulham is in position 2.
  - Newcastle United is in position 3.
  - Chelsea is in position 4.
  - Ties are handled by assigning the same position.

- **Tier Assignment**:
  - Tier 1: Top 33% of teams based on points.
  - Tier 2: Middle 33% of teams.
  - Tier 3: Bottom 34% of teams.

### Thought Process

1. **Calculate Points**:
   - Compute points for each team using the formula: `wins * 3 + draws * 1`.

2. **Rank Teams**:
   - Use the `RANK()` function to assign positions based on points.
   - Handle ties appropriately by assigning the same position.

3. **Assign Tiers**:
   - Determine the total number of teams.
   - Divide the teams into three tiers using the `CEIL()` function.
   - Assign tiers based on position, placing ties in the higher tier.

4. **Order Results**:
   - Sort the results by points in descending order, then by team_name in ascending order.

### Query
```sql
WITH cte AS (
    SELECT 
        team_id,
        team_name,
        wins * 3 + draws * 1 AS points
    FROM 
        TeamStats
),

cte2 AS (
    SELECT 
        *,
        RANK() OVER (ORDER BY points DESC) AS position,
        COUNT(*) OVER () AS total_teams
    FROM 
        cte
)

SELECT 
    team_name,
    points,
    position,
    CASE 
        WHEN position <= CEIL(total_teams / 3.0) THEN 'Tier 1'
        WHEN position <= CEIL(2 * total_teams / 3.0) THEN 'Tier 2'
        ELSE 'Tier 3'
    END AS tier
FROM 
    cte2
ORDER BY 
    points DESC,
    team_name;
