### Count Student Number in Departments

Table: Student

```sql
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| student_id   | int     |
| student_name | varchar |
| gender       | varchar |
| dept_id      | int     |
+--------------+---------+
```
`student_id` is the primary key (column with unique values) for this table.
`dept_id` is a foreign key (reference column) to `dept_id` in the `Department` table.
Each row of this table indicates the name of a student, their gender, and the id of their department.

Table: Department

```sql
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| dept_id     | int     |
| dept_name   | varchar |
+-------------+---------+
```
`dept_id` is the primary key (column with unique values) for this table.
Each row of this table contains the id and the name of a department.

Write a solution to report the respective department name and number of students majoring in each department for all departments in the `Department` table (even ones with no current students).

Return the result table ordered by `student_number` in descending order. In case of a tie, order them by `dept_name` alphabetically.

### Example:

#### Input:

`Student` table:

```sql
+------------+--------------+--------+---------+
| student_id | student_name | gender | dept_id |
+------------+--------------+--------+---------+
| 1          | Jack         | M      | 1       |
| 2          | Jane         | F      | 1       |
| 3          | Mark         | M      | 2       |
+------------+--------------+--------+---------+
```

`Department` table:

```sql
+---------+-------------+
| dept_id | dept_name   |
+---------+-------------+
| 1       | Engineering |
| 2       | Science     |
| 3       | Law         |
+---------+-------------+
```

#### Output:

```sql
+-------------+----------------+
| dept_name   | student_number |
+-------------+----------------+
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |
+-------------+----------------+
```

### Thought Process:
1. **Understand the Requirements**:
   - We need a list of all departments, even those without students, along with the count of students in each department.
   - The result must be sorted by the number of students (`student_number`) in descending order and alphabetically by `dept_name` in case of ties.

2. **Approach**:
   - Use a `LEFT JOIN` between the `Department` and `Student` tables to ensure all departments are included.
   - Count the number of students for each department using the `COUNT` function.
   - Handle departments with no students by ensuring they appear with a count of `0`.
   - Use `GROUP BY` on `dept_name` to calculate the count for each department.
   - Sort the results by `student_number` in descending order and then by `dept_name` alphabetically.

3. **SQL Features Used**:
   - `LEFT JOIN` to include all departments.
   - `COUNT` function to calculate the number of students.
   - `ORDER BY` for sorting the results.

### Query:

```sql
SELECT 
    d.dept_name AS dept_name, 
    COUNT(s.student_id) AS student_number
FROM 
    Department d
LEFT JOIN 
    Student s 
ON 
    d.dept_id = s.dept_id
GROUP BY 
    d.dept_name
ORDER BY 
    student_number DESC, 
    dept_name;
```

### Explanation of the Query:
1. **Join Tables**:
   - Use a `LEFT JOIN` to combine the `Department` and `Student` tables, ensuring all departments are included, even if they have no students.

2. **Count Students**:
   - `COUNT(s.student_id)` calculates the number of students in each department. For departments with no students, this will return `0`.

3. **Group Results**:
   - `GROUP BY d.dept_name` ensures we aggregate the results by department.

4. **Sort Results**:
   - `ORDER BY student_number DESC` sorts departments by the number of students in descending order.
   - `dept_name` ensures alphabetical sorting for departments with the same number of students.

### Output:
The query produces the desired output with department names and the count of students, sorted as specified. For example:

```sql
+-------------+----------------+
| dept_name   | student_number |
+-------------+----------------+
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |
+-------------+----------------+
