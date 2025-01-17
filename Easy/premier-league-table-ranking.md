### Premier League Table Ranking

#### Table: TeamStats

```plaintext
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| team_id          | int     |
| team_name        | varchar |
| matches_played   | int     |
| wins             | int     |
| draws            | int     |
| losses           | int     |
+------------------+---------+
```
- `team_id` is the unique key for this table.
- This table contains `team_id`, `team_name`, `matches_played`, `wins`, `draws`, and `losses`.

Write a solution to calculate the points and rank for each team in the league. Points are calculated as follows:
- 3 points for a win
- 1 point for a draw
- 0 points for a loss

**Note:** Teams with the same points must be assigned the same rank.

Return the result table ordered by points in descending, and then by `team_name` in ascending order.

The query result format is in the following example:

### Example:

**Input:**

TeamStats table:

```plaintext
+---------+-----------------+----------------+------+-------+--------+
| team_id | team_name       | matches_played | wins | draws | losses |
+---------+-----------------+----------------+------+-------+--------+
| 1       | Manchester City | 10             | 6    | 2     | 2      |
| 2       | Liverpool       | 10             | 6    | 2     | 2      |
| 3       | Chelsea         | 10             | 5    | 3     | 2      |
| 4       | Arsenal         | 10             | 4    | 4     | 2      |
| 5       | Tottenham       | 10             | 3    | 5     | 2      |
+---------+-----------------+----------------+------+-------+--------+
```

**Output:**

```plaintext
+---------+-----------------+--------+----------+
| team_id | team_name       | points | position |
+---------+-----------------+--------+----------+
| 2       | Liverpool       | 20     | 1        |
| 1       | Manchester City | 20     | 1        |
| 3       | Chelsea         | 18     | 3        |
| 4       | Arsenal         | 16     | 4        |
| 5       | Tottenham       | 14     | 5        |
+---------+-----------------+--------+----------+
```

### Explanation:

- **Manchester City** and **Liverpool** both have 20 points (`6 wins * 3 points + 2 draws * 1 point`), so they share position `1`.
- **Chelsea** has 18 points (`5 wins * 3 points + 3 draws * 1 point`) and is in position `3`.
- **Arsenal** has 16 points (`4 wins * 3 points + 4 draws * 1 point`) and is in position `4`.
- **Tottenham** has 14 points (`3 wins * 3 points + 5 draws * 1 point`) and is in position `5`.

The output table is ordered by points in descending order, then by `team_name` in ascending order.

---

### Thought Process:

1. **Understand the Requirement**:
   - Calculate total points for each team based on the formula:
     - Points = (3 * Wins) + (1 * Draws) + (0 * Losses)
   - Assign a rank to teams with the same points.
   - Order by points descending and `team_name` ascending.

2. **Steps to Solve**:
   - Use a **Common Table Expression (CTE)** to calculate the points for each team.
   - Use the `RANK()` window function to assign ranks based on points.
   - Order the results as required.

3. **Query Output**:
   - The query will return `team_id`, `team_name`, `points`, and `position` for each team.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        team_id,
        team_name,
        (3 * wins) + (1 * draws) + (0 * losses) AS points
    FROM 
        TeamStats
)

SELECT 
    team_id,
    team_name,
    points,
    RANK() OVER (ORDER BY points DESC) AS position
FROM 
    cte
ORDER BY 
    points DESC, 
    team_name ASC;
```

---

### Explanation of the Query:

1. **Common Table Expression (CTE)**:
   - Calculate the `points` for each team using the formula `(3 * wins) + (1 * draws) + (0 * losses)`.
   - The result includes `team_id`, `team_name`, and `points`.

2. **Ranking Teams**:
   - Use `RANK()` to assign ranks based on points in descending order.
   - Teams with the same points receive the same rank.

3. **Final Ordering**:
   - Order the output by `points` in descending order.
   - If points are the same, sort by `team_name` in ascending order.

---

### Why This Query Works:
- The `RANK()` function ensures proper ranking even with ties.
- The CTE simplifies the calculation of points for better readability.
- The final ordering meets the problem's requirements for sorting by points and `team_name`.

---
