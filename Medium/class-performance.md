### Class Performance

We are given a table:

#### Table: Scores
| Column Name  | Type    |
|--------------|---------|
| student_id   | int     |
| student_name | varchar |
| assignment1  | int     |
| assignment2  | int     |
| assignment3  | int     |

- `student_id` is the column of unique values.
- Each row contains:
  - `student_id`: The ID of the student.
  - `student_name`: The name of the student.
  - `assignment1`, `assignment2`, `assignment3`: The scores the student received in three assignments.

#### Task
Write a query to calculate the difference between the highest total score (sum of all 3 assignments) and the lowest total score obtained by students.

The result should include:
- `difference_in_score`: The calculated difference.

The result can be returned in any order.

---

### Example Input/Output

##### Input:
**Scores table:**
| student_id | student_name | assignment1 | assignment2 | assignment3 |
|------------|--------------|-------------|-------------|-------------|
| 309        | Owen         | 88          | 47          | 87          |
| 321        | Claire       | 98          | 95          | 37          |
| 338        | Julian       | 100         | 64          | 43          |
| 423        | Peyton       | 60          | 44          | 47          |
| 896        | David        | 32          | 37          | 50          |
| 235        | Camila       | 31          | 53          | 69          |

##### Output:
| difference_in_score |
|---------------------|
| 111                 |

##### Explanation:
- **Total Scores:**
  - Student 309: `88 + 47 + 87 = 222`
  - Student 321: `98 + 95 + 37 = 230`
  - Student 338: `100 + 64 + 43 = 207`
  - Student 423: `60 + 44 + 47 = 151`
  - Student 896: `32 + 37 + 50 = 119`
  - Student 235: `31 + 53 + 69 = 153`
- The highest total score is `230` (student 321).
- The lowest total score is `119` (student 896).
- The difference is `230 - 119 = 111`.

---

### Thought Process
1. **Calculate Total Scores:**
   - Compute the total score for each student as the sum of `assignment1`, `assignment2`, and `assignment3`.
2. **Find Maximum and Minimum Scores:**
   - Use `MAX()` to get the highest total score.
   - Use `MIN()` to get the lowest total score.
3. **Calculate Difference:**
   - Subtract the minimum score from the maximum score to find the difference.

---

### Query
```sql
WITH cte AS (
    SELECT student_id,
           student_name,
           assignment1 + assignment2 + assignment3 AS total_score
    FROM scores
    GROUP BY student_id, student_name
),

cte2 AS (
    SELECT MAX(total_score) AS max_score
    FROM cte
),

cte3 AS (
    SELECT MIN(total_score) AS min_score
    FROM cte
)

SELECT max_score - min_score AS difference_in_score 
FROM cte2, cte3;
```

---

### Key Takeaways
- **Aggregation Functions:**
  - `MAX()` and `MIN()` help identify the highest and lowest total scores.
- **With Clause:**
  - Common Table Expressions (CTEs) simplify the calculation by breaking the query into manageable parts.
- **Grouping:**
  - Grouping by `student_id` and `student_name` ensures total scores are calculated for each student individually.

---

### Related Topics
- SQL Aggregation (`MAX`, `MIN`, `SUM`)
- Common Table Expressions (CTEs)
- Arithmetic Operations in SQL
