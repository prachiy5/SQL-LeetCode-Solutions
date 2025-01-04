# The Winner University
### Problem Description
The task is to determine the winner of a competition between New York University and California University based on the number of excellent students (students scoring 90 or more). The result should indicate the winning university, or "No Winner" if there is a tie.

---

### Input Tables

#### NewYork Table
| student_id | score |
|------------|-------|
| int        | int   |

#### California Table
| student_id | score |
|------------|-------|
| int        | int   |

---

### Query
```sql
WITH 
-- Count excellent students in New York University
newyork AS (
    SELECT COUNT(student_id) AS newyork
    FROM NewYork 
    WHERE score >= 90
),
-- Count excellent students in California University
california AS (
    SELECT COUNT(student_id) AS california
    FROM California 
    WHERE score >= 90
)
-- Compare the counts and determine the winner
SELECT 
    CASE 
        WHEN newyork > california THEN 'New York University'
        WHEN california > newyork THEN 'California University'
        ELSE 'No Winner'
    END AS winner
FROM 
    newyork, california;
```

---

### Thought Process
1. **Identify Excellent Students**:
   - Define "excellent students" as those with scores >= 90.
   - Use `COUNT` to calculate the number of excellent students in each table.

2. **Compare Counts**:
   - Use a `CASE` statement to compare the counts from New York and California tables.
   - Assign the appropriate winner based on the comparison.

3. **Result**:
   - If New York has more excellent students, return "New York University".
   - If California has more excellent students, return "California University".
   - If they have the same number of excellent students, return "No Winner".

---

### Explanation
1. **WITH Clause**:
   - `newyork`: Counts the number of excellent students in New York University.
   - `california`: Counts the number of excellent students in California University.

2. **SELECT with CASE**:
   - Compares the results of the `newyork` and `california` CTEs.
   - Determines the winner or if it's a draw.

3. **Output**:
   - Returns a single row with the `winner` column indicating the result.

---

### Example Output
#### Input:
**NewYork Table:**
| student_id | score |
|------------|-------|
| 1          | 90    |
| 2          | 87    |

**California Table:**
| student_id | score |
|------------|-------|
| 2          | 89    |
| 3          | 88    |

#### Output:
| winner              |
|---------------------|
| New York University |

---

### Key Takeaways
- Use CTEs to simplify counting logic for each university.
- Use `CASE` to handle conditional logic for determining the winner.
- Ensure clear comparison logic for all cases, including ties.
