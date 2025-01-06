### Find Top Scoring Students II:

Table: students

| Column Name | Type    |
|-------------|---------|
| student_id  | int     |
| name        | varchar |
| major       | varchar |

- `student_id` is the primary key for this table.
- Each row contains the student ID, student name, and their major.

Table: courses

| Column Name | Type    |
|-------------|---------|
| course_id   | int     |
| name        | varchar |
| credits     | int     |
| major       | varchar |
| mandatory   | enum    |

- `course_id` is the primary key for this table.
- `mandatory` is an enum type of (`Yes`, `No`).
- Each row contains the course ID, course name, credits, major it belongs to, and whether the course is mandatory.

Table: enrollments

| Column Name | Type    |
|-------------|---------|
| student_id  | int     |
| course_id   | int     |
| semester    | varchar |
| grade       | varchar |
| GPA         | decimal |

- `(student_id, course_id, semester)` is the primary key for this table.
- Each row contains the student ID, course ID, semester, and grade received.

Write a solution to find the students who meet the following criteria:

1. Have taken all mandatory courses and at least two elective courses offered in their major.
2. Achieved a grade of `A` in all mandatory courses and at least `B` in elective courses.
3. Maintained an average GPA of at least `2.5` across all their courses (including those outside their major).

Return the result table ordered by `student_id` in ascending order.

---

### Example:

#### Input:

`students` table:

| student_id | name     | major            |
|------------|----------|------------------|
| 1          | Alice    | Computer Science |
| 2          | Bob      | Computer Science |
| 3          | Charlie  | Mathematics      |
| 4          | David    | Mathematics      |

`courses` table:

| course_id | name              | credits | major            | mandatory |
|-----------|-------------------|---------|------------------|-----------|
| 101       | Algorithms        | 3       | Computer Science | yes       |
| 102       | Data Structures   | 3       | Computer Science | yes       |
| 103       | Calculus          | 4       | Mathematics      | yes       |
| 104       | Linear Algebra    | 4       | Mathematics      | yes       |
| 105       | Machine Learning  | 3       | Computer Science | no        |
| 106       | Probability       | 3       | Mathematics      | no        |
| 107       | Operating Systems | 3       | Computer Science | no        |
| 108       | Statistics        | 3       | Mathematics      | no        |

`enrollments` table:

| student_id | course_id | semester    | grade | GPA |
|------------|-----------|-------------|-------|-----|
| 1          | 101       | Fall 2023   | A     | 4.0 |
| 1          | 102       | Spring 2023 | A     | 4.0 |
| 1          | 105       | Spring 2023 | A     | 4.0 |
| 1          | 107       | Fall 2023   | B     | 3.5 |
| 2          | 101       | Fall 2023   | A     | 4.0 |
| 2          | 102       | Spring 2023 | B     | 3.0 |
| 3          | 103       | Fall 2023   | A     | 4.0 |
| 3          | 104       | Spring 2023 | A     | 4.0 |
| 3          | 106       | Spring 2023 | A     | 4.0 |
| 3          | 108       | Fall 2023   | B     | 3.5 |
| 4          | 103       | Fall 2023   | B     | 3.0 |
| 4          | 104       | Spring 2023 | B     | 3.0 |

#### Output:

| student_id |
|------------|
| 1          |
| 3          |

#### Explanation:

- **Alice (student_id 1)**: A Computer Science major who has taken all mandatory courses (Algorithms and Data Structures) with an `A` and at least two electives (Machine Learning and Operating Systems) with an `A` and `B`. Maintains an average GPA of 3.875.
- **Bob (student_id 2)**: Did not receive an `A` in all mandatory courses, so does not qualify.
- **Charlie (student_id 3)**: A Mathematics major who has taken all mandatory courses (Calculus and Linear Algebra) with an `A` and at least two electives (Probability and Statistics) with an `A` and `B`. Maintains an average GPA of 3.875.
- **David (student_id 4)**: Did not receive an `A` in all mandatory courses, so does not qualify.

---

### Thought Process:

1. **Calculate Average GPA**:
   - Ensure each student has a GPA of at least 2.5.

2. **Filter Mandatory Courses**:
   - Check that all mandatory courses in the student's major have been completed with an `A` grade.

3. **Filter Elective Courses**:
   - Ensure the student has completed at least two elective courses in their major with at least a `B` grade.

4. **Combine Criteria**:
   - Combine the above conditions to identify students who satisfy all requirements.

5. **Output Results**:
   - Return the list of qualifying `student_id` values, ordered by `student_id` in ascending order.

---

### Query:

```sql
WITH
    T AS (
        SELECT student_id
        FROM enrollments
        GROUP BY 1
        HAVING AVG(GPA) >= 2.5
    )
SELECT student_id
FROM
    T
    JOIN students USING (student_id)
    JOIN courses USING (major)
    LEFT JOIN enrollments USING (student_id, course_id)
GROUP BY 1
HAVING
    SUM(mandatory = 'yes' AND grade = 'A') = SUM(mandatory = 'yes')
    AND SUM(mandatory = 'no' AND grade IS NOT NULL) = SUM(mandatory = 'no' AND grade IN ('A', 'B'))
    AND SUM(mandatory = 'no' AND grade IS NOT NULL) >= 2
ORDER BY 1;
