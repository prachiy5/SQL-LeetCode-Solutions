### Find Candidates for Data Scientist Position

**Table: Candidates**

```
+--------------+---------+ 
| Column Name  | Type    | 
+--------------+---------+ 
| candidate_id | int     | 
| skill        | varchar |
+--------------+---------+
(candidate_id, skill) is the primary key (columns with unique values) for this table.
Each row includes candidate_id and skill.
```

Write a query to find the candidates best suited for a Data Scientist position. The candidate must be proficient in **Python**, **Tableau**, and **PostgreSQL**.

Return the result table ordered by `candidate_id` in ascending order.

### Example

**Input:**

**Candidates table:**

```
+---------------+--------------+
| candidate_id  | skill        | 
+---------------+--------------+
| 123           | Python       |
| 234           | R            | 
| 123           | Tableau      | 
| 123           | PostgreSQL   | 
| 234           | PowerBI      | 
| 234           | SQL Server   | 
| 147           | Python       | 
| 147           | Tableau      | 
| 147           | Java         |
| 147           | PostgreSQL   |
| 256           | Tableau      |
| 102           | DataAnalysis |
+---------------+--------------+
```

**Output:**

```
+--------------+
| candidate_id |  
+--------------+
| 123          |  
| 147          | 
+--------------+
```

**Explanation:**
- Candidates `123` and `147` possess the necessary skills in **Python**, **Tableau**, and **PostgreSQL** for the data scientist position.
- Candidates `234` and `102` do not possess any of the required skills for this position.
- Candidate `256` has proficiency in Tableau but is missing skills in Python and PostgreSQL.
The output table is sorted by `candidate_id` in ascending order.

---

### Thought Process

1. **Understand the Requirements:**
   - We need to identify candidates who have all three required skills (**Python**, **Tableau**, **PostgreSQL**).
   - Only candidates with all three skills should be included in the result.

2. **Approach:**
   - Use the `WHERE` clause to filter rows where `skill` is one of the required skills.
   - Group the data by `candidate_id`.
   - Use the `HAVING` clause to ensure each candidate has all three required skills.
   - Order the result by `candidate_id` in ascending order.

3. **Key Considerations:**
   - We need to count the distinct skills for each candidate to verify if they match all three required skills.
   - The result must be sorted by `candidate_id`.

---

### Query

```sql
SELECT candidate_id 
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(*) = 3
ORDER BY candidate_id;
```

### Explanation of the Query

1. **Filter Rows:**
   - The `WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')` clause selects only the rows where the skill is one of the required ones.

2. **Group Data:**
   - The `GROUP BY candidate_id` groups rows by `candidate_id`, allowing us to analyze each candidate's skills collectively.

3. **Check for All Skills:**
   - The `HAVING COUNT(*) = 3` ensures only candidates with all three required skills are included.

4. **Order Results:**
   - The `ORDER BY candidate_id` sorts the result in ascending order of `candidate_id`.

---

### Why This Works

- **Candidate `123`:** Has all three skills (**Python**, **Tableau**, **PostgreSQL**).
- **Candidate `147`:** Has all three skills (**Python**, **Tableau**, **PostgreSQL**).
- **Other Candidates:** Do not meet the skill requirements, so they are excluded.

The output correctly identifies and lists candidates best suited for the Data Scientist position.
