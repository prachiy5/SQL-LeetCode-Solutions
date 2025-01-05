### Find Cutoff Score for Each School

#### Tables:

**`Schools` Table:**

| Column Name | Type |
|-------------|------|
| school_id   | int  |
| capacity    | int  |

- **`school_id`** is the column with unique values for this table.
- This table contains information about the capacity of some schools. The **`capacity`** is the maximum number of students the school can accept.

**`Exam` Table:**

| Column Name   | Type |
|---------------|------|
| score         | int  |
| student_count | int  |

- **`score`** is the column with unique values for this table.
- Each row in this table indicates that there are **`student_count`** students that scored at least **`score`** points in the exam.
- The data is logically correct, meaning a row recording a higher score will have the same or smaller **`student_count`** compared to a row recording a lower score. Formally, for every two rows `i` and `j` in the table, if **`score[i] > score[j]`** then **`student_count[i] <= student_count[j]`**.

#### Problem Statement:

Every year, each school announces a **minimum score requirement** that a student needs to apply. The school selects the minimum score requirement based on the following conditions:

1. The school wants to ensure that even if every student meeting the requirement applies, the school can accept all applications.
2. The school wants to maximize the possible number of students that can apply.
3. The score must be chosen from the `Exam` table.
4. If multiple scores satisfy the above conditions, the **smallest score** is chosen.
5. If the input data is not enough to determine the score, report **`-1`**.

Return the result table with the **`school_id`** and the chosen **`score`** for each school.

#### Output:

The result table should be in any order and formatted as follows:

| school_id | score |
|-----------|-------|
| 5         | 975   |
| 9         | -1    |
| 10        | 749   |
| 11        | 744   |

---

### Thought Process

To determine the minimum score requirement for each school:

1. **Understand the Problem Logic:**
   - We need to find the maximum possible number of students that a school can accommodate based on its capacity.
   - Among the scores satisfying this condition, choose the smallest possible score.

2. **Join the Tables:**
   - Perform a join between the `Schools` and `Exam` tables where **`student_count <= capacity`** to ensure the school can accommodate all students meeting that score.

3. **Group by School:**
   - Group the data by `school_id` and find the minimum score for each school.

4. **Handle Schools Without Valid Scores:**
   - For schools that do not have any valid score satisfying the conditions, return **`-1`**.

5. **Write the Query:**
   - Use conditional logic and aggregation functions to compute the result.

---

### Query

```sql
SELECT 
    school_id, 
    IFNULL(MIN(score), -1) AS score
FROM 
    schools 
LEFT JOIN 
    exam
ON 
    student_count <= capacity
GROUP BY 
    school_id;
