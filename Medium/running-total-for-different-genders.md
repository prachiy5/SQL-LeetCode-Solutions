### Running Total for Different Genders
We are given a table:

#### Table: `Scores`

| Column Name   | Type    |
|---------------|---------|
| player_name   | varchar |
| gender        | varchar |
| day           | date    |
| score_points  | int     |

- `(gender, day)` is the primary key.
- Each row indicates that a player with the given `player_name` and `gender` scored `score_points` on a specific `day`.
- `gender` is `'F'` for female players and `'M'` for male players.

### Task
Write a SQL query to calculate the cumulative total score for each gender on each day. The result should be:
1. Ordered by `gender` in ascending order.
2. For each `gender`, ordered by `day` in ascending order.

### Example
#### Input:
**Scores table:**

| player_name | gender | day        | score_points |
|-------------|--------|------------|--------------|
| Aron        | F      | 2020-01-01 | 17           |
| Alice       | F      | 2020-01-07 | 23           |
| Bajrang     | M      | 2020-01-07 | 7            |
| Khali       | M      | 2019-12-25 | 11           |
| Slaman      | M      | 2019-12-30 | 13           |
| Joe         | M      | 2019-12-31 | 3            |
| Jose        | M      | 2019-12-18 | 2            |
| Priya       | F      | 2019-12-31 | 23           |
| Priyanka    | F      | 2019-12-30 | 17           |

#### Output:

| gender | day        | total |
|--------|------------|-------|
| F      | 2019-12-30 | 17    |
| F      | 2019-12-31 | 40    |
| F      | 2020-01-01 | 57    |
| F      | 2020-01-07 | 80    |
| M      | 2019-12-18 | 2     |
| M      | 2019-12-25 | 13    |
| M      | 2019-12-30 | 26    |
| M      | 2019-12-31 | 29    |
| M      | 2020-01-07 | 36    |

#### Explanation:
- For each `gender`, calculate the cumulative total score by summing up `score_points` in order of `day`.
- Example for females:
  - `2019-12-30`: Priyanka scored `17`, cumulative total: `17`.
  - `2019-12-31`: Priya scored `23`, cumulative total: `17 + 23 = 40`.
  - `2020-01-01`: Aron scored `17`, cumulative total: `40 + 17 = 57`.
  - `2020-01-07`: Alice scored `23`, cumulative total: `57 + 23 = 80`.

---

### Thought Process
1. **Partition by Gender:** Use a `PARTITION BY` clause to separate data for male and female players.
2. **Order by Day:** Within each gender, sort the rows by `day`.
3. **Cumulative Sum:** Use the `SUM()` window function to calculate the cumulative total of `score_points` for each gender.
4. **Result Order:** Ensure the result is ordered by `gender` and `day` as specified.

---

### SQL Query
```sql
SELECT 
    gender,
    day,
    SUM(score_points) OVER (PARTITION BY gender ORDER BY day) AS total
FROM Scores;
```

---

### Key Takeaways
1. **Window Functions:** The `SUM()` function with `OVER` allows for cumulative calculations.
2. **Partitioning:** `PARTITION BY` ensures that the calculation is performed separately for each gender.
3. **Ordering Within Partition:** `ORDER BY` within the window function ensures cumulative totals are calculated in chronological order.
4. **Result Order:** The result is inherently ordered by `gender` and `day` as required.

### Related Topics
- Window Functions in SQL
- Partitioning and Ordering in SQL
- Cumulative Calculations
