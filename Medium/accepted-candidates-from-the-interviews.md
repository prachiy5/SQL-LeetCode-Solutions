### Accepted Candidates From the Interviews

#### Table: Candidates

```sql
+--------------+----------+
| Column Name  | Type     |
+--------------+----------+
| candidate_id | int      |
| name         | varchar  |
| years_of_exp | int      |
| interview_id | int      |
+--------------+----------+
```
`candidate_id` is the primary key (column with unique values) for this table. Each row of this table indicates the name of a candidate, their number of years of experience, and their interview ID.

#### Table: Rounds

```sql
+--------------+------+ 
| Column Name  | Type |
+--------------+------+ 
| interview_id | int  |
| round_id     | int  |
| score        | int  |
+--------------+------+
```
`(interview_id, round_id)` is the primary key (combination of columns with unique values) for this table. Each row of this table indicates the score of one round of an interview.

#### Problem Statement

Write a solution to report the IDs of the candidates who have at least two years of experience and the sum of the score of their interview rounds is strictly greater than 15.

Return the result table in any order.

### Example Input

#### Candidates table:

```sql
+--------------+---------+--------------+--------------+
| candidate_id | name    | years_of_exp | interview_id |
+--------------+---------+--------------+--------------+
| 11           | Atticus | 1            | 101          |
| 9            | Ruben   | 6            | 104          |
| 6            | Aliza   | 10           | 109          |
| 8            | Alfredo | 0            | 107          |
+--------------+---------+--------------+--------------+
```

#### Rounds table:

```sql
+--------------+----------+-------+
| interview_id | round_id | score |
+--------------+----------+-------+
| 109          | 3        | 4     |
| 101          | 2        | 8     |
| 109          | 4        | 1     |
| 107          | 1        | 3     |
| 104          | 3        | 6     |
| 109          | 1        | 4     |
| 104          | 4        | 7     |
| 104          | 1        | 2     |
| 109          | 2        | 1     |
| 104          | 2        | 7     |
| 107          | 2        | 3     |
| 101          | 1        | 8     |
+--------------+----------+-------+
```

#### Output:

```sql
+--------------+
| candidate_id |
+--------------+
| 9            |
+--------------+
```

#### Explanation:
- Candidate 11: The total score is 16, and they have one year of experience. We do not include them in the result table because of their years of experience.
- Candidate 9: The total score is 22, and they have six years of experience. We include them in the result table.
- Candidate 6: The total score is 10, and they have ten years of experience. We do not include them in the result table because the score is not good enough.
- Candidate 8: The total score is 6, and they have zero years of experience. We do not include them in the result table because of their years of experience and the score.

### Thought Process
1. **Aggregate Scores:** Use a Common Table Expression (CTE) to calculate the total score for each `interview_id` by summing the scores from the `Rounds` table.
2. **Filter Candidates:** Join the aggregated scores with the `Candidates` table using `interview_id` to align scores with candidates.
3. **Apply Conditions:** Filter candidates who have at least 2 years of experience and whose total interview score is strictly greater than 15.
4. **Select Output:** Return the `candidate_id` of candidates meeting the criteria.

### Query

```sql
WITH cte AS (
    SELECT interview_id, SUM(score) AS total_score
    FROM rounds
    GROUP BY interview_id
)

SELECT candidate_id
FROM candidates ca
INNER JOIN cte ct
ON ca.interview_id = ct.interview_id
WHERE years_of_exp >= 2 
  AND total_score > 15;
