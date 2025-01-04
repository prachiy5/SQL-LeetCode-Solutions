### All the Matches of the League
**Table: Teams**

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| team_name   | varchar |
+-------------+---------+
```
- `team_name` is the column with unique values of this table.
- Each row of this table shows the name of a team.

Write a solution to report all the possible matches of the league. Note that every two teams play two matches with each other, with one team being the `home_team` once and the other time being the `away_team`.

Return the result table in any order.

### Example:

#### Input:

**Teams table:**

```
+-------------+
| team_name   |
+-------------+
| Leetcode FC |
| Ahly SC     |
| Real Madrid |
+-------------+
```

#### Output:

```
+-------------+-------------+
| home_team   | away_team   |
+-------------+-------------+
| Real Madrid | Leetcode FC |
| Real Madrid | Ahly SC     |
| Leetcode FC | Real Madrid |
| Leetcode FC | Ahly SC     |
| Ahly SC     | Real Madrid |
| Ahly SC     | Leetcode FC |
+-------------+-------------+
```

#### Explanation:
All the matches of the league are shown in the table. Each pair of teams plays two matches: one with one team as the `home_team` and the other as the `away_team`.

---

### Thought Process:

1. **Understand the Problem**: We need to generate all possible combinations of matches between teams where each pair plays twice (once as `home_team` and once as `away_team`).

2. **Key Insights**:
   - Use a `CROSS JOIN` to generate all combinations of teams.
   - Filter out cases where the `home_team` and `away_team` are the same.

3. **Steps to Solve**:
   - Perform a `CROSS JOIN` on the `Teams` table to pair every team with every other team.
   - Use a condition to ensure that the `home_team` is not the same as the `away_team`.
   - Return the `home_team` and `away_team` for all valid combinations.

4. **Result**:
   - The output will contain all valid matches where each pair of teams plays twice.

---

### Query:

```sql
SELECT 
    t1.team_name AS home_team,
    t2.team_name AS away_team
FROM 
    Teams t1
CROSS JOIN 
    Teams t2
WHERE 
    t1.team_name != t2.team_name;
```

---

### Explanation of the Query:

1. **CROSS JOIN**:
   - The `CROSS JOIN` pairs every row from `Teams t1` with every row from `Teams t2`. This ensures all possible combinations of teams are generated.

2. **Filter Condition**:
   - `WHERE t1.team_name != t2.team_name` ensures that the `home_team` and `away_team` are not the same team.

3. **Column Selection**:
   - The query selects `t1.team_name` as the `home_team` and `t2.team_name` as the `away_team` for all valid combinations.

4. **Result**:
   - The query returns all matches where each pair of teams plays twice, with roles reversed in the second match.

---

### Why this Works:

- The `CROSS JOIN` ensures all team combinations are covered.
- The `WHERE` clause filters out invalid matches (where a team plays against itself).
- The result contains all valid matches as required by the problem.

---

### Example Walkthrough:

#### Input:

**Teams table:**

```
+-------------+
| team_name   |
+-------------+
| Leetcode FC |
| Ahly SC     |
| Real Madrid |
+-------------+
```

#### Steps:
1. Generate all combinations using `CROSS JOIN`:

```
+-------------+-------------+
| home_team   | away_team   |
+-------------+-------------+
| Leetcode FC | Leetcode FC |
| Leetcode FC | Ahly SC     |
| Leetcode FC | Real Madrid |
| Ahly SC     | Leetcode FC |
| Ahly SC     | Ahly SC     |
| Ahly SC     | Real Madrid |
| Real Madrid | Leetcode FC |
| Real Madrid | Ahly SC     |
| Real Madrid | Real Madrid |
+-------------+-------------+
```

2. Apply the filter `WHERE t1.team_name != t2.team_name`:

```
+-------------+-------------+
| home_team   | away_team   |
+-------------+-------------+
| Leetcode FC | Ahly SC     |
| Leetcode FC | Real Madrid |
| Ahly SC     | Leetcode FC |
| Ahly SC     | Real Madrid |
| Real Madrid | Leetcode FC |
| Real Madrid | Ahly SC     |
+-------------+-------------+
```

---

### Final Output:

```
+-------------+-------------+
| home_team   | away_team   |
+-------------+-------------+
| Real Madrid | Leetcode FC |
| Real Madrid | Ahly SC     |
| Leetcode FC | Real Madrid |
| Leetcode FC | Ahly SC     |
| Ahly SC     | Real Madrid |
| Ahly SC     | Leetcode FC |
+-------------+-------------+
