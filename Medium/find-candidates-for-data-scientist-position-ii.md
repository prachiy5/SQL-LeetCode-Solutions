# Find Candidates for Data Scientist Position II
You are given two tables:

### Candidates Table
| Column Name  | Type    |
|--------------|---------|
| candidate_id | int     |
| skill        | varchar |
| proficiency  | int     |

- `(candidate_id, skill)` is the unique key for this table.
- Each row includes the `candidate_id`, the skill they possess, and their proficiency level (1-5).

### Projects Table
| Column Name  | Type    |
|--------------|---------|
| project_id   | int     |
| skill        | varchar |
| importance   | int     |

- `(project_id, skill)` is the primary key for this table.
- Each row includes the `project_id`, a required skill for the project, and its importance level (1-5).

### Task
Leetcode is staffing multiple data science projects. Your task is to find the best candidate for each project based on the following criteria:

1. Candidates **must have all the skills required** for a project.
2. Calculate a **score** for each candidate-project pair as follows:
   - Start with 100 points.
   - Add 10 points for each skill where `proficiency > importance`.
   - Subtract 5 points for each skill where `proficiency < importance`.
3. Include **only the top candidate** (highest score) for each project:
   - If there’s a tie, choose the candidate with the lower `candidate_id`.
   - If no candidate satisfies all the required skills, exclude the project from the result.

Return the result table ordered by `project_id` in ascending order.

### Input Example
#### Candidates Table:
| candidate_id | skill     | proficiency |
|--------------|-----------|-------------|
| 101          | Python    | 5           |
| 101          | Tableau   | 3           |
| 101          | PostgreSQL| 4           |
| 101          | TensorFlow| 2           |
| 102          | Python    | 4           |
| 102          | Tableau   | 5           |
| 102          | PostgreSQL| 4           |
| 102          | R         | 4           |
| 103          | Python    | 3           |
| 103          | Tableau   | 5           |
| 103          | PostgreSQL| 5           |
| 103          | Spark     | 4           |

#### Projects Table:
| project_id  | skill     | importance |
|-------------|-----------|------------|
| 501         | Python    | 4          |
| 501         | Tableau   | 3          |
| 501         | PostgreSQL| 5          |
| 502         | Python    | 3          |
| 502         | Tableau   | 4          |
| 502         | R         | 2          |

### Output Example
| project_id  | candidate_id | score |
|-------------|--------------|-------|
| 501         | 101          | 105   |
| 502         | 102          | 130   |

### Explanation

1. **Project 501**:
   - Required skills: `Python`, `Tableau`, `PostgreSQL`.
   - Candidates:
     - Candidate 101: Score = 100 + 10 (Python) + 0 (Tableau) - 5 (PostgreSQL) = 105.
     - Candidate 102: Score = 100 + 0 (Python) + 10 (Tableau) - 5 (PostgreSQL) = 105.
     - Candidate 103: Score = 100 - 5 (Python) + 10 (Tableau) + 10 (PostgreSQL) = 115.
   - Candidate 103 has the highest score but does not have all required skills.
   - Candidate 101 and 102 have the same score, so choose the one with the lower `candidate_id` (101).

2. **Project 502**:
   - Required skills: `Python`, `Tableau`, `R`.
   - Candidates:
     - Candidate 102: Score = 100 + 10 (Python) + 10 (Tableau) + 10 (R) = 130.
     - Candidate 103: Does not have the skill `R`.
   - Candidate 102 is the only suitable candidate with all required skills.

### Thought Process
1. **Count Required Skills per Project**:
   - Use a CTE to calculate the number of skills required for each project (`cnt_skills_required`).

2. **Find Common Skills**:
   - Perform an inner join between `Projects` and `Candidates` to find matching skills.
   - Check for candidates who match all required skills of a project by comparing their skill count with `cnt_skills_required`.
   - Calculate the score for each candidate-project pair.

3. **Rank Candidates**:
   - Use `ROW_NUMBER()` to rank candidates for each project based on their score (descending).
   - In case of ties, use `candidate_id` (ascending).

4. **Select Top Candidates**:
   - Filter for the top-ranked candidate (`r=1`) for each project.

5. **Order Results**:
   - Sort the final result by `project_id` in ascending order.

### Query
```sql
WITH project_skills AS (
    SELECT 
        project_id,
        COUNT(*) AS cnt_skills_required
    FROM projects
    GROUP BY project_id
),

common_skills AS (
    SELECT 
        candidate_id,
        project_id,
        COUNT(*) AS common_skill,
        100 + SUM(
            CASE 
                WHEN proficiency > importance THEN 10
                WHEN proficiency < importance THEN -5
                ELSE 0
            END
        ) AS score
    FROM projects p
    INNER JOIN candidates c
        ON p.skill = c.skill
    GROUP BY candidate_id, project_id
),

cte AS (
    SELECT 
        cs.project_id,
        cs.candidate_id,
        cs.score, 
        ROW_NUMBER() OVER (
            PARTITION BY cs.project_id 
            ORDER BY cs.score DESC, cs.candidate_id
        ) AS r
    FROM common_skills cs
    INNER JOIN project_skills ps
        ON cs.project_id = ps.project_id
        AND common_skill = cnt_skills_required
)

SELECT 
    project_id,
    candidate_id,
    score
FROM cte
WHERE r = 1
ORDER BY project_id;
