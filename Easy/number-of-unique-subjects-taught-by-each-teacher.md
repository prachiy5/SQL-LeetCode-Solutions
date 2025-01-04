
###  Number of Unique Subjects Taught by Each Teacher

**Table**: Teacher

| Column Name  | Type |
|--------------|------|
| teacher_id   | int  |
| subject_id   | int  |
| dept_id      | int  |

(subject_id, dept_id) is the primary key (combination of columns with unique values) of this table.
Each row in this table indicates that the teacher with `teacher_id` teaches the subject `subject_id` in the department `dept_id`.

Write a solution to calculate the number of unique subjects each teacher teaches in the university.

**Return the result table in any order.**

The result format is shown in the following example.

---
### Example:

**Input:**

Teacher table:

| teacher_id | subject_id | dept_id |
|------------|------------|---------|
| 1          | 2          | 3       |
| 1          | 2          | 4       |
| 1          | 3          | 3       |
| 2          | 1          | 1       |
| 2          | 2          | 1       |
| 2          | 3          | 1       |
| 2          | 4          | 1       |

**Output:**

| teacher_id | cnt |
|------------|-----|
| 1          | 2   |
| 2          | 4   |

**Explanation:**

- **Teacher 1**:
  - They teach subject `2` in departments `3` and `4`.
  - They teach subject `3` in department `3`.
  - Unique subjects = `2`.

- **Teacher 2**:
  - They teach subject `1` in department `1`.
  - They teach subject `2` in department `1`.
  - They teach subject `3` in department `1`.
  - They teach subject `4` in department `1`.
  - Unique subjects = `4`.

---
### Thought Process:

1. **Understand the Requirement**:
   - We need to count the number of unique `subject_id` values for each `teacher_id`.
   - A teacher may teach the same subject in different departments, but the subject should only be counted once.

2. **Key Points**:
   - Use the `DISTINCT` keyword to ensure only unique `subject_id` values are counted.
   - Use the `GROUP BY` clause to group the data by `teacher_id`.

3. **Approach**:
   - Select `teacher_id` and the count of distinct `subject_id` values.
   - Group the data by `teacher_id`.
   - Return the result with the count of unique subjects (`cnt`) for each teacher.

4. **Result**:
   - The output table should display `teacher_id` and the corresponding count of unique subjects (`cnt`).

---
### Query:

```sql
SELECT 
    teacher_id, 
    COUNT(DISTINCT subject_id) AS cnt 
FROM 
    teacher
GROUP BY 
    teacher_id;
```

---
### Explanation of the Query:

1. **Select Unique Subjects**:
   - `COUNT(DISTINCT subject_id)` ensures that only unique subjects for each teacher are counted.

2. **Group by Teacher**:
   - `GROUP BY teacher_id` groups the data by each teacher, so the count is calculated per teacher.

3. **Return the Result**:
   - The result contains `teacher_id` and the count of unique subjects (`cnt`).

---
### Why This Output?

- **Teacher 1**:
  - Teaches `2` unique subjects (`2`, `3`).

- **Teacher 2**:
  - Teaches `4` unique subjects (`1`, `2`, `3`, `4`).

Thus, the output reflects the correct count of unique subjects for each teacher.
