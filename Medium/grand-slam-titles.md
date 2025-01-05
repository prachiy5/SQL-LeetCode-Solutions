### Grand Slam Titles

**Table: Players**
```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| player_id      | int     |
| player_name    | varchar |
+----------------+---------+
```
player_id is the primary key (column with unique values) for this table.
Each row in this table contains the name and the ID of a tennis player.

**Table: Championships**
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| year          | int     |
| Wimbledon     | int     |
| Fr_open       | int     |
| US_open       | int     |
| Au_open       | int     |
+---------------+---------+
```
year is the primary key (column with unique values) for this table.
Each row of this table contains the IDs of the players who won one each tennis tournament of the grand slam.

Write a solution to report the number of grand slam tournaments won by each player. Do not include the players who did not win any tournament.

Return the result table in any order.

### Example

**Input:**

**Players table:**

```
+-----------+-------------+
| player_id | player_name |
+-----------+-------------+
| 1         | Nadal       |
| 2         | Federer     |
| 3         | Novak       |
+-----------+-------------+
```

**Championships table:**

```
+------+-----------+---------+---------+---------+
| year | Wimbledon | Fr_open | US_open | Au_open |
+------+-----------+---------+---------+---------+
| 2018 | 1         | 1       | 1       | 1       |
| 2019 | 1         | 1       | 2       | 2       |
| 2020 | 2         | 1       | 2       | 2       |
+------+-----------+---------+---------+---------+
```

**Output:**

```
+-----------+-------------+-------------------+
| player_id | player_name | grand_slams_count |
+-----------+-------------+-------------------+
| 2         | Federer     | 5                 |
| 1         | Nadal       | 7                 |
+-----------+-------------+-------------------+
```

**Explanation:**
Player 1 (Nadal) won 7 titles: Wimbledon (2018, 2019), Fr_open (2018, 2019, 2020), US_open (2018), and Au_open (2018).
Player 2 (Federer) won 5 titles: Wimbledon (2020), US_open (2019, 2020), and Au_open (2019, 2020).
Player 3 (Novak) did not win anything, we did not include them in the result table.

---

### Thought Process

1. The data in the `Championships` table is spread across columns for different tournaments (Wimbledon, Fr_open, US_open, and Au_open). To count the total grand slam wins, the data needs to be reshaped into a unified format where each row represents a single win.
2. Use a `UNION ALL` query to transform the `Championships` table, consolidating all tournaments into one column (`player_id`) while preserving the year and the grand slam name.
3. Join the reshaped data with the `Players` table to get the player names.
4. Group the data by `player_id` and `player_name` to count the total wins for each player.
5. Exclude players with zero wins by ensuring only players appearing in the reshaped data are included.

---

### Query

```sql
WITH cte AS (
    SELECT year, 'Wimbeldon' AS grand_slam, Wimbledon AS player_id
    FROM Championships
    UNION ALL
    SELECT year, 'Fr_open' AS grand_slam, Fr_open AS player_id
    FROM Championships
    UNION ALL
    SELECT year, 'US_open' AS grand_slam, US_open AS player_id
    UNION ALL
    SELECT year, 'Au_open' AS grand_slam, Au_open AS player_id
    FROM Championships
)

SELECT p.player_id, p.player_name, COUNT(c.player_id) AS grand_slams_count
FROM cte c
INNER JOIN Players p
    ON p.player_id = c.player_id
GROUP BY p.player_id, p.player_name;
