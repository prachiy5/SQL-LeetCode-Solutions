### Winning Candidate

Table: Candidate

```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| name        | varchar  |
+-------------+----------+
```
`id` is the column with unique values for this table.
Each row of this table contains information about the id and the name of a candidate.

Table: Vote

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| candidateId | int  |
+-------------+------+
```
`id` is an auto-increment primary key (column with unique values).
`candidateId` is a foreign key (reference column) to `id` from the `Candidate` table.
Each row of this table determines the candidate who got the ith vote in the elections.

Write a solution to report the name of the winning candidate (i.e., the candidate who got the largest number of votes).

The test cases are generated so that exactly one candidate wins the elections.

The result format is in the following example.

#### Example 1:

**Input:**  
Candidate table:

```
+----+------+
| id | name |
+----+------+
| 1  | A    |
| 2  | B    |
| 3  | C    |
| 4  | D    |
| 5  | E    |
+----+------+
```

Vote table:

```
+----+-------------+
| id | candidateId |
+----+-------------+
| 1  | 2           |
| 2  | 4           |
| 3  | 3           |
| 4  | 2           |
| 5  | 5           |
+----+-------------+
```

**Output:**  

```
+------+
| name |
+------+
| B    |
+------+
```

**Explanation:**  
Candidate B has 2 votes. Candidates C, D, and E have 1 vote each. The winner is candidate B.

---

### Thought Process:

1. **Understand the Requirement:**  
   - Find the candidate who received the maximum number of votes.
   - Join the `Vote` table with the `Candidate` table to map `candidateId` to the candidate's name.
   - Count the votes for each candidate using `GROUP BY`.
   - Retrieve the name of the candidate with the maximum vote count.

2. **Approach:**
   - Use a `WITH` clause (Common Table Expression, CTE) to count the votes for each candidate.
   - Select the name of the candidate whose vote count matches the maximum vote count in the CTE.

3. **Key Steps:**
   - Perform an `INNER JOIN` between `Candidate` and `Vote` on `candidateId`.
   - Use `COUNT` to calculate the total votes for each candidate.
   - Use `MAX` to determine the highest vote count.
   - Filter for the candidate(s) with the maximum votes.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        c.name, 
        v.candidateId, 
        COUNT(v.candidateId) AS total_votes
    FROM candidate c
    INNER JOIN vote v
    ON c.id = v.candidateId
    GROUP BY c.name, v.candidateId
)

SELECT name 
FROM cte
WHERE total_votes = (SELECT MAX(total_votes) FROM cte);
```

---

### Explanation:

1. **Step 1: Join the Tables**  
   - Join `Candidate` and `Vote` tables on `candidateId` to map the votes to the candidate names.

2. **Step 2: Count Votes**  
   - Use `COUNT(v.candidateId)` to calculate the total votes for each candidate.
   - Group by `c.name` and `v.candidateId` to ensure each candidate's votes are grouped correctly.

3. **Step 3: Find Maximum Votes**  
   - Use a subquery with `MAX(total_votes)` to find the highest vote count.

4. **Step 4: Filter by Maximum Votes**  
   - Select the candidate whose `total_votes` matches the maximum votes.

5. **Step 5: Return the Winner**  
   - Return the name of the winning candidate.

---

### Output:

For the input provided:

```
+------+ 
| name | 
+------+ 
| B    | 
+------+ 
