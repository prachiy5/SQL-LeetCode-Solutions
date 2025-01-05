### Tournament Winners

We are given two tables, `Players` and `Matches`:

#### `Players` Table:
- `player_id`: Primary key indicating a unique player.
- `group_id`: The group to which the player belongs.

#### `Matches` Table:
- `match_id`: Primary key indicating a unique match.
- `first_player`: The ID of the first player in the match.
- `second_player`: The ID of the second player in the match.
- `first_score`: Points scored by the first player.
- `second_score`: Points scored by the second player.

The task is to determine the winner in each group based on the total points scored by players within their group. The winner is defined as:
1. The player with the maximum total points within the group.
2. In case of a tie, the player with the smallest `player_id` is the winner.

Return the result table in any order.

### Example Input

#### `Players` Table:
| player_id | group_id |
|-----------|----------|
| 15        | 1        |
| 25        | 1        |
| 30        | 1        |
| 45        | 1        |
| 10        | 2        |
| 35        | 2        |
| 50        | 2        |
| 20        | 3        |
| 40        | 3        |

#### `Matches` Table:
| match_id | first_player | second_player | first_score | second_score |
|----------|--------------|---------------|-------------|--------------|
| 1        | 15           | 45            | 3           | 0            |
| 2        | 30           | 25            | 1           | 2            |
| 3        | 30           | 15            | 2           | 0            |
| 4        | 40           | 20            | 5           | 2            |
| 5        | 35           | 50            | 1           | 1            |

### Example Output
| group_id | player_id |
|----------|-----------|
| 1        | 15        |
| 2        | 35        |
| 3        | 40        |

### Thought Process
1. **Combine Player Scores**:
   - Use a `UNION ALL` query to gather all scores from both `first_player` and `second_player` along with their points.
2. **Calculate Total Scores**:
   - Group by `player_id` and sum their scores to compute total points for each player.
3. **Associate Players with Groups**:
   - Join the `Players` table with the total scores table to map each player to their group.
4. **Determine Group Winners**:
   - Use `DENSE_RANK` partitioned by `group_id` to rank players by their total scores in descending order. In case of a tie, the player with the smallest `player_id` ranks higher.
5. **Filter Winners**:
   - Select only the top-ranked player from each group.

### Query
```sql
WITH cte AS (
    SELECT first_player AS player, first_score AS score FROM matches
    UNION ALL
    SELECT second_player AS player, second_score AS score FROM matches
),

cte2 AS (
    SELECT player, SUM(score) AS total_score
    FROM cte
    GROUP BY player
),

cte3 AS (
    SELECT p.player_id, p.group_id, c.total_score,
           DENSE_RANK() OVER (PARTITION BY p.group_id ORDER BY c.total_score DESC, p.player_id) AS r
    FROM players p
    INNER JOIN cte2 c
    ON p.player_id = c.player
    ORDER BY p.group_id
)

SELECT group_id, player_id
FROM cte3
WHERE r = 1;
