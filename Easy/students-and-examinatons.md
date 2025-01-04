# Student and Examinations

### Problem

#### Table Overview

**Students**

| Column Name   | Type    |
|---------------|---------|
| student_id    | int     |
| student_name  | varchar |

- `student_id` is the primary key.
- Each row contains the ID and name of a student.

**Subjects**

| Column Name  | Type    |
|--------------|---------|
| subject_name | varchar |

- `subject_name` is the primary key.
- Each row represents a unique subject.

**Examinations**

| Column Name  | Type    |
|--------------|---------|
| student_id   | int     |
| subject_name | varchar |

- This table records exams attended by students.
- It may contain duplicate rows.

#### Task
Find the number of times each student attended each exam for all students and subjects.
1. Include all students and all subjects, even if they did not attend any exam.
2. Return the result ordered by `student_id` and `subject_name`.

---

### Example

**Input:**

`Students` table:

| student_id | student_name |
|------------|--------------|
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |

`Subjects` table:

| subject_name |
|--------------|
| Math         |
| Physics      |
| Programming  |

`Examinations` table:

| student_id | subject_name |
|------------|--------------|
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |

**Output:**

| student_id | student_name | subject_name | attended_exams |
|------------|--------------|--------------|----------------|
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |

---

### Thought Process

1. **Cross Join Students and Subjects:**
   - Use a `CROSS JOIN` to combine all students with all subjects. This ensures we include all combinations of students and subjects, even if there are no records in the `Examinations` table.

2. **Left Join with Examinations:**
   - Use a `LEFT JOIN` to match the student-subject combinations with the `Examinations` table. This allows us to count the number of exams attended, including cases where no exams were attended.

3. **Count Exams:**
   - Use `COUNT()` to calculate the number of times a student attended an exam for each subject.

4. **Group and Order:**
   - Group by `student_id` and `subject_name` to calculate the count for each combination.
   - Order the result by `student_id` and `subject_name`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
    s.student_id, 
    s.student_name, 
    sub.subject_name, 
    COUNT(e.subject_name) AS attended_exams
FROM Students s 
CROSS JOIN Subjects sub
LEFT JOIN Examinations e
    ON s.student_id = e.student_id AND sub.subject_name = e.subject_name
GROUP BY 
    s.student_id, 
    s.student_name, 
    sub.subject_name
ORDER BY 
    s.student_id, 
    sub.subject_name;
```

---

### Solution Explanation

1. **Cross Join Students and Subjects:**
   - Generates all possible combinations of `student_id` and `subject_name`.

2. **Left Join with Examinations:**
   - Matches each combination with the `Examinations` table using both `student_id` and `subject_name`.

3. **Count Exams:**
   - `COUNT(e.subject_name)` counts the number of times a student attended the exam for a subject. If no match is found, the count is `0`.

4. **Group and Order:**
   - Group by `student_id` and `subject_name` to ensure the count is calculated for each combination.
   - Order by `student_id` and `subject_name` for a structured result.

---

### Key Takeaways

- **CROSS JOIN:** Ensures all combinations of students and subjects are included.
- **LEFT JOIN:** Allows for matching while retaining unmatched rows.
- **COUNT():** Handles counting rows, including cases with no matches.

---

### Related Topics
- SQL Joins and Aggregations
- Counting in SQL
- Combining Data from Multiple Tables
