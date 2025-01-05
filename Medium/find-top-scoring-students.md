### Find Top Scoring Students

Table: **students**
```
+-------------+----------+
| Column Name | Type     | 
+-------------+----------+
| student_id  | int      |
| name        | varchar  |
| major       | varchar  |
+-------------+----------+
```

**student_id** is the primary key (combination of columns with unique values) for this table.
Each row of this table contains the student ID, student name, and their major.

Table: **courses**
```
+-------------+----------+
| Column Name | Type     | 
+-------------+----------+
| course_id   | int      |
| name        | varchar  |
| credits     | int      |
| major       | varchar  |
+-------------+----------+
```
**course_id** is the primary key (combination of columns with unique values) for this table.
Each row of this table contains the course ID, course name, the number of credits for the course, and the major it belongs to.

Table: **enrollments**
```
+-------------+----------+
| Column Name | Type     | 
+-------------+----------+
| student_id  | int      |
| course_id   | int      |
| semester    | varchar  |
| grade       | varchar  |
+-------------+----------+
```
**(student_id, course_id, semester)** is the primary key (combination of columns with unique values) for this table.
Each row of this table contains the student ID, course ID, semester, and grade received.

Write a solution to find the students who have taken **all courses offered in their major** and have achieved a grade of **A** in all these courses.

Return the result table ordered by **student_id** in ascending order.

### Example:

**Input:**

**students** table:
```
+------------+------------------+------------------+
| student_id | name             | major            |
+------------+------------------+------------------+
| 1          | Alice            | Computer Science |
| 2          | Bob              | Computer Science |
| 3          | Charlie          | Mathematics      |
| 4          | David            | Mathematics      |
+------------+------------------+------------------+
```
**courses** table:
```
+-----------+-----------------+---------+------------------+
| course_id | name            | credits | major            |
+-----------+-----------------+---------+------------------+
| 101       | Algorithms      | 3       | Computer Science |
| 102       | Data Structures | 3       | Computer Science |
| 103       | Calculus        | 4       | Mathematics      |
| 104       | Linear Algebra  | 4       | Mathematics      |
+-----------+-----------------+---------+------------------+
```
**enrollments** table:
```
+------------+-----------+----------+-------+
| student_id | course_id | semester | grade |
+------------+-----------+----------+-------+
| 1          | 101       | Fall 2023| A     |
| 1          | 102       | Fall 2023| A     |
| 2          | 101       | Fall 2023| B     |
| 2          | 102       | Fall 2023| A     |
| 3          | 103       | Fall 2023| A     |
| 3          | 104       | Fall 2023| A     |
| 4          | 103       | Fall 2023| A     |
| 4          | 104       | Fall 2023| B     |
+------------+-----------+----------+-------+
```
**Output:**
```
+------------+
| student_id |
+------------+
| 1          |
| 3          |
+------------+
```
**Explanation:**

- Alice (student_id 1) is a Computer Science major and has taken both "Algorithms" and "Data Structures", receiving an 'A' in both.
- Bob (student_id 2) is a Computer Science major but did not receive an 'A' in all required courses.
- Charlie (student_id 3) is a Mathematics major and has taken both "Calculus" and "Linear Algebra", receiving an 'A' in both.
- David (student_id 4) is a Mathematics major but did not receive an 'A' in all required courses.

The output table is ordered by **student_id** in ascending order.

---

### Thought Process

1. **Determine the total required courses for each student**:
   - Join the `students` table with the `courses` table using the `major` column to count how many courses are required for each student's major.

2. **Count courses where the student achieved an 'A'**:
   - Join the `students` table with `enrollments` and `courses` using `student_id` and `major` to count how many courses a student took and got an 'A'.

3. **Compare the two counts**:
   - If the total courses taken and graded 'A' match the total required courses, include the student in the output.

4. **Order the results**:
   - Sort the output by `student_id` in ascending order.

---

### Query

```sql
WITH required_courses AS (
    SELECT s.student_id, COUNT(c.course_id) AS total_required_courses
    FROM students s
    JOIN courses c ON s.major = c.major
    GROUP BY s.student_id
),

completed_courses AS (
    SELECT s.student_id, COUNT(e.course_id) AS total_a_courses
    FROM students s
    JOIN enrollments e ON s.student_id = e.student_id
    JOIN courses c ON e.course_id = c.course_id AND s.major = c.major
    WHERE e.grade = 'A'
    GROUP BY s.student_id
)

SELECT rc.student_id
FROM required_courses rc
JOIN completed_courses cc
    ON rc.student_id = cc.student_id
WHERE rc.total_required_courses = cc.total_a_courses
ORDER BY rc.student_id;
