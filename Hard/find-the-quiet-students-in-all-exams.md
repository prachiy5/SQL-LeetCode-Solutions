### Find the Quiet Students in All Exams

Table: Student
```
+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| student_id          | int     |
| student_name        | varchar |
+---------------------+---------+
```
student_id is the primary key (column with unique values) for this table.
student_name is the name of the student.

Table: Exam
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| exam_id       | int     |
| student_id    | int     |
| score         | int     |
+---------------+---------+
```
(exam_id, student_id) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates that the student with student_id had a score points in the exam with id exam_id.

A quiet student is the one who took at least one exam and did not score the highest or the lowest score.

Write a solution to report the students (student_id, student_name) being quiet in all exams. Do not return the student who has never taken any exam.

Return the result table ordered by student_id.

The result format is in the following example.

#### Example 1:

**Input:**

Student table:
```
+-------------+---------------+
| student_id  | student_name  |
+-------------+---------------+
| 1           | Daniel        |
| 2           | Jade          |
| 3           | Stella        |
| 4           | Jonathan      |
| 5           | Will          |
+-------------+---------------+
```
Exam table:
```
+------------+--------------+-----------+
| exam_id    | student_id   | score     |
+------------+--------------+-----------+
| 10         |     1        |    70     |
| 10         |     2        |    80     |
| 10         |     3        |    90     |
| 20         |     1        |    80     |
| 30         |     1        |    70     |
| 30         |     3        |    80     |
| 30         |     4        |    90     |
| 40         |     1        |    60     |
| 40         |     2        |    70     |
| 40         |     4        |    80     |
+------------+--------------+-----------+
```
**Output:**
```
+-------------+---------------+
| student_id  | student_name  |
+-------------+---------------+
| 2           | Jade          |
+-------------+---------------+
```
**Explanation:**
- For exam 10: Student 1 and 3 hold the lowest and high scores respectively.
- For exam 20: Student 1 holds both highest and lowest scores.
- For exam 30 and 40: Student 1 and 4 hold the lowest and high scores respectively.
- Student 2 and 5 have never got the highest or lowest in any of the exams.
- Since student 5 is not taking any exam, he is excluded from the result.
  So, we only return the information of Student 2.

### Thought Process
1. Identify the maximum and minimum scores for each exam.
2. Determine students who scored either the maximum or minimum in any exam.
3. Filter out students who are not in the group identified in step 2, ensuring they took at least one exam.
4. Return the remaining students' IDs and names, ordered by student ID.

### Query
```sql
WITH cte AS (
    SELECT 
        s.student_id,
        student_name,
        exam_id,
        score, 
        MAX(score) OVER(PARTITION BY exam_id) AS max_score,
        MIN(score) OVER(PARTITION BY exam_id) AS min_score
    FROM student s 
    INNER JOIN exam e
    ON s.student_id = e.student_id
),

cte2 AS (
    SELECT DISTINCT student_id, student_name
    FROM cte 
    WHERE score = max_score OR score = min_score
)

SELECT DISTINCT student_id, student_name
FROM cte 
WHERE student_id NOT IN (SELECT student_id FROM cte2)
ORDER BY student_id;
