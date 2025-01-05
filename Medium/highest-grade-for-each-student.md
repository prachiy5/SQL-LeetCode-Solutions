### Highest Grade For Each Student
We are given a table:

#### Table: `Enrollments`

| Column Name   | Type |
|---------------|------|
| student_id    | int  |
| course_id     | int  |
| grade         | int  |

- `(student_id, course_id)` is the primary key, meaning it uniquely identifies each row.
- The `grade` column is never NULL.

### Task
Write a SQL query to find the highest grade along with its corresponding course for each student. If there is a tie in grades (multiple courses with the same highest grade), return the course with the smallest `course_id`.

The result table should be ordered by `student_id` in ascending order.

### Example
#### Input:
**Enrollments table:**

| student_id | course_id | grade |
|------------|-----------|-------|
| 2          | 2         | 95    |
| 2          | 3         | 95    |
| 1          | 1         | 90    |
| 1          | 2         | 99    |
| 3          | 1         | 80    |
| 3          | 2         | 75    |
| 3          | 3         | 82    |

#### Output:

| student_id | course_id | grade |
|------------|-----------|-------|
| 1          | 2         | 99    |
| 2          | 2         | 95    |
| 3          | 3         | 82    |

#### Explanation:
- For `student_id = 1`, the highest grade is `99` in `course_id = 2`.
- For `student_id = 2`, the highest grade is `95` in both `course_id = 2` and `course_id = 3`. The course with the smallest `course_id` is `2`.
- For `student_id = 3`, the highest grade is `82` in `course_id = 3`.

---

### Thought Process
1. **Rank Grades**: Use the `DENSE_RANK()` window function to rank grades for each student in descending order, partitioned by `student_id`.
2. **Filter Top Grades**: Identify rows where the rank is `1`, representing the highest grade for each student.
3. **Resolve Ties**: Use `MIN(course_id)` to ensure that in case of a tie, the course with the smallest `course_id` is selected.
4. **Group and Order**: Group by `student_id` and `grade`, and order the final result by `student_id`.

---

### SQL Query
```sql
WITH cte AS (
    SELECT 
        student_id,
        course_id,
        grade,
        DENSE_RANK() OVER (PARTITION BY student_id ORDER BY grade DESC) AS r
    FROM Enrollments
)

SELECT 
    student_id,
    MIN(course_id) AS course_id,
    grade
FROM cte
WHERE r = 1
GROUP BY student_id, grade
ORDER BY student_id;
```

---

### Key Takeaways
1. Use `DENSE_RANK()` to rank rows within partitions based on specific criteria (e.g., highest grade).
2. Handle ties by applying aggregation functions like `MIN()` to select the smallest value in such cases.
3. Combine `WITH` clauses (CTEs) with grouping and filtering for clearer, more modular queries.
4. Always ensure the result is ordered as required by the task.

### Related Topics
- Window Functions
- Ranking Functions (`DENSE_RANK`, `RANK`)
- Aggregation
- Common Table Expressions (CTEs)
