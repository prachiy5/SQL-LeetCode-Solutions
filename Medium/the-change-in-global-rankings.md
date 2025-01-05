### The Change in Global Rankings

#### Table: TeamPoints
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| team_id     | int     |
| name        | varchar |
| points      | int     |
+-------------+---------+
```
team_id contains unique values.  
Each row of this table contains the ID of a national team, the name of the country it represents, and the points it has in the global rankings. No two teams will represent the same country.

#### Table: PointsChange
```
+---------------+------+
| Column Name   | Type |
+---------------+------+
| team_id       | int  |
| points_change | int  |
+---------------+------+
```
team_id contains unique values.  
Each row of this table contains the ID of a national team and the change in its points in the global rankings.  
points_change can be:  
- 0: indicates no change in points.  
- positive: indicates an increase in points.  
- negative: indicates a decrease in points.  
Each team_id that appears in TeamPoints will also appear in this table.

---

The global ranking of a national team is its rank after sorting all the teams by their points in descending order. If two teams have the same points, we break the tie by sorting them by their name in lexicographical order.

The points of each national team should be updated based on its corresponding points_change value.

Write a solution to calculate the change in the global rankings after updating each team's points.

---

#### Return the result table in any order.

---

### Example Input:

#### TeamPoints table:

| team_id | name        | points |
|---------|-------------|--------|
| 3       | Algeria     | 1431   |
| 1       | Senegal     | 2132   |
| 2       | New Zealand | 1402   |
| 4       | Croatia     | 1817   |

#### PointsChange table:

| team_id | points_change |
|---------|---------------|
| 3       | 399           |
| 2       | 0             |
| 4       | 13            |
| 1       | -22           |

---

### Output:

| team_id | name        | rank_diff |
|---------|-------------|-----------|
| 1       | Senegal     | 0         |
| 4       | Croatia     | -1        |
| 3       | Algeria     | 1         |
| 2       | New Zealand | 0         |

---

### Thought Process:

1. First, join the `TeamPoints` and `PointsChange` tables to combine the points and their respective changes.
2. Calculate the updated points for each team by adding the `points` and `points_change`.
3. Determine the old and new ranks for each team based on their points:
   - Use the `RANK()` function to assign ranks sorted by points in descending order and names in lexicographical order in case of ties.
4. Calculate the difference between the old and new ranks for each team to determine the `rank_diff`.
5. Return the `team_id`, `name`, and `rank_diff`.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        tp.team_id,
        name,
        points,
        points_change 
    FROM 
        TeamPoints tp
    INNER JOIN 
        PointsChange pc
    ON 
        tp.team_id = pc.team_id
),

cte2 AS (
    SELECT 
        team_id,
        name, 
        points AS old_points,
        points + points_change AS updated_points,
        RANK() OVER (ORDER BY points DESC, name) AS old_rank,
        RANK() OVER (ORDER BY points + points_change DESC, name) AS new_rank
    FROM 
        cte
)

SELECT 
    team_id, name,
    CAST(old_rank AS SIGNED) - CAST(new_rank AS SIGNED) AS rank_diff
FROM 
    cte2;
