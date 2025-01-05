### Election Results
We are given a table:

#### Table: Votes
| Column Name | Type    |
|-------------|---------|
| voter       | varchar |
| candidate   | varchar |

- `(voter, candidate)` is the primary key.
- Each row represents a voter and the candidate they voted for.
- Voters can vote for multiple candidates, but their vote is equally divided among those candidates. If a voter votes for two candidates, each candidate receives 0.5 votes.
- Voters may also choose not to vote for any candidate, in which case their vote does not contribute to any candidate.

#### Task
Write a query to find the candidate(s) who received the most votes and won the election. If multiple candidates received the same highest number of votes, return all of their names in ascending order.

---

### Example Input/Output

##### Input:
**Votes table:**
| voter    | candidate |
|----------|-----------|
| Kathy    | null      |
| Charles  | Ryan      |
| Charles  | Christine |
| Charles  | Kathy     |
| Benjamin | Christine |
| Anthony  | Ryan      |
| Edward   | Ryan      |
| Terry    | null      |
| Evelyn   | Kathy     |
| Arthur   | Christine |

##### Output:
| candidate |
|-----------|
| Christine |
| Ryan      |

##### Explanation:
- Kathy and Terry did not vote, so their votes are not counted.
- Charles voted for three candidates (Ryan, Christine, and Kathy), giving each 0.33 votes.
- Benjamin, Arthur, Anthony, Edward, and Evelyn each voted for one candidate, giving them 1 vote each.
- Total votes for each candidate:
  - Christine: 0.33 (Charles) + 1 (Benjamin) + 1 (Arthur) = 2.33
  - Ryan: 0.33 (Charles) + 1 (Anthony) + 1 (Edward) = 2.33
  - Kathy: 0.33 (Charles) + 1 (Evelyn) = 1.33
- Christine and Ryan received the most votes (2.33 each), so they are both winners. Their names are returned in ascending order.

---

### Thought Process
1. **Count Candidates per Voter:**
   - Use a `COUNT()` window function to determine how many candidates each voter voted for.
2. **Calculate Vote Share:**
   - Divide 1 by the number of candidates for each voter to calculate the vote share each candidate receives.
   - Exclude rows where `candidate IS NULL` because those voters did not participate.
3. **Sum Votes by Candidate:**
   - Use `SUM()` to calculate the total votes each candidate received.
4. **Identify Winners:**
   - Find the maximum vote count.
   - Select candidates whose total votes match the maximum.
5. **Order Results:**
   - Return the candidate names in ascending order.

---

### Query
```sql
WITH cte AS (
    -- Count how many candidates each voter voted for
    SELECT voter, candidate, 
           COUNT(candidate) OVER (PARTITION BY voter) AS candidate_count
    FROM votes
),

cte2 AS (
    -- Calculate the vote for each candidate (1 divided by candidate count)
    SELECT voter, candidate, 
           1.0 / candidate_count AS vote_share
    FROM cte
    WHERE candidate IS NOT NULL
),

cte3 AS (
    -- Sum the votes for each candidate
    SELECT candidate, 
           SUM(vote_share) AS total_votes
    FROM cte2
    GROUP BY candidate
)

-- Select the candidate(s) with the maximum votes, ordered alphabetically
SELECT candidate
FROM cte3
WHERE total_votes = (SELECT MAX(total_votes) AS max_votes
    FROM cte3)
ORDER BY candidate;
```

---

### Key Takeaways
- **Window Functions:** The `COUNT()` window function allows us to calculate the number of candidates each voter voted for.
- **Conditional Aggregation:** Filtering with `WHERE candidate IS NOT NULL` ensures non-participating voters are excluded from calculations.
- **Subqueries and CTEs:** Breaking the problem into smaller steps with CTEs simplifies calculations and makes the query more readable.
- **Tie Handling:** Using `ORDER BY candidate` ensures tied candidates are displayed in alphabetical order.

---

### Related Topics
- SQL Window Functions
- Common Table Expressions (CTEs)
- Aggregate Functions (`SUM`, `COUNT`)
- Conditional Filtering and Ordering
