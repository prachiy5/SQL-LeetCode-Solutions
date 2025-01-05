### Compute the Rank as a Percentage

We are given a table:

#### Table: Students
| Column Name   | Type |
|---------------|------|
| student_id    | int  |
| department_id | int  |
| mark          | int  |

- `student_id` contains unique values.
- Each row indicates a student's ID, the department ID they belong to, and their mark in the exam.

#### Task
Write a query to calculate the rank of each student in their department as a percentage, using the following formula:

\[(\text{student\_rank\_in\_the\_department} - 1) \times 100 / (\text{the\_number\_of\_students\_in\_the\_department} - 1)\]

The percentage should:
- Be rounded to 2 decimal places.
- Handle ties in ranks (students with the same marks get the same rank).

Return the result table in any order.

#### Example Input/Output

##### Input:
**Students table:**
| student_id | department_id | mark |
|------------|---------------|------|
| 2          | 2             | 650  |
| 8          | 2             | 650  |
| 7          | 1             | 920  |
| 1          | 1             | 610  |
| 3          | 1             | 530  |

##### Output:
| student_id | department_id | percentage |
|------------|---------------|------------|
| 7          | 1             | 0.0        |
| 1          | 1             | 50.0       |
| 3          | 1             | 100.0      |
| 2          | 2             | 0.0        |
| 8          | 2             | 0.0        |

##### Explanation:
- **For Department 1:**
  - Student 7: \[(1 - 1) \times 100 / (3 - 1) = 0.0\]
  - Student 1: \[(2 - 1) \times 100 / (3 - 1) = 50.0\]
  - Student 3: \[(3 - 1) \times 100 / (3 - 1) = 100.0\]

- **For Department 2:**
  - Student 2: \[(1 - 1) \times 100 / (2 - 1) = 0.0\]
  - Student 8: \[(1 - 1) \times 100 / (2 - 1) = 0.0\]

---

### Thought Process
1. Use a Common Table Expression (CTE) to rank students in each department based on their `mark` in descending order. Use the `RANK()` function to handle ties appropriately.
2. In a second CTE, calculate the total number of students in each department.
3. Join the two CTEs and calculate the rank percentage for each student using the provided formula.
4. Use `ROUND()` to ensure the percentage is rounded to 2 decimal places.
5. Handle edge cases where a department has only one student by returning 0% for the percentage.

---

### Query
```sql
WITH cte AS (
    SELECT *,
           RANK() OVER (PARTITION BY department_id ORDER BY mark DESC) AS rn
    FROM students
),

cte2 AS (
    SELECT department_id,
           COUNT(student_id) AS total_students_in_dept 
    FROM cte
    GROUP BY department_id
)

SELECT DISTINCT student_id, 
                cte.department_id,
                IFNULL(ROUND((rn - 1) * 100 / (total_students_in_dept - 1), 2), 0) AS percentage 
FROM cte
JOIN cte2
ON cte.department_id = cte2.department_id;
```

---

### Key Takeaways
- **Window Functions:** The `RANK()` function is used to rank students based on marks within each department.
- **CTEs:** Common Table Expressions simplify the query by breaking it into logical steps.
- **Edge Cases:** For departments with a single student, the formula avoids division by zero by returning 0%.
- **Formatting:** The `ROUND()` function ensures percentages are rounded to 2 decimal places for clarity.

---

### Related Topics
- SQL Window Functions (e.g., `RANK()`, `ROW_NUMBER()`)
- Common Table Expressions (CTEs)
- Aggregate Functions
- Conditional Logic in SQL
