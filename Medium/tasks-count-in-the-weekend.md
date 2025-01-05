### Tasks Count in the Weekend

```sql
Table: Tasks

+-------------+------+
| Column Name | Type |
+-------------+------+
| task_id     | int  |
| assignee_id | int  |
| submit_date | date |
+-------------+------+
task_id is the primary key (column with unique values) for this table.
Each row in this table contains the ID of a task, the id of the assignee, and the submission date.
```

### Problem Statement

Write a solution to report:

1. The number of tasks that were submitted during the weekend (Saturday, Sunday) as `weekend_cnt`, and
2. The number of tasks that were submitted during the working days as `working_cnt`.

Return the result table in any order.

### Example Input

```sql
Tasks table:
+---------+-------------+-------------+
| task_id | assignee_id | submit_date |
+---------+-------------+-------------+
| 1       | 1           | 2022-06-13  |
| 2       | 6           | 2022-06-14  |
| 3       | 6           | 2022-06-15  |
| 4       | 3           | 2022-06-18  |
| 5       | 5           | 2022-06-19  |
| 6       | 7           | 2022-06-19  |
+---------+-------------+-------------+
```

### Example Output

```sql
+-------------+-------------+
| weekend_cnt | working_cnt |
+-------------+-------------+
| 3           | 3           |
+-------------+-------------+
```

### Explanation

- Task 1 was submitted on Monday.
- Task 2 was submitted on Tuesday.
- Task 3 was submitted on Wednesday.
- Task 4 was submitted on Saturday.
- Task 5 was submitted on Sunday.
- Task 6 was submitted on Sunday.

Three tasks were submitted during the weekend, and three tasks were submitted during the working days.

---

### Thought Process

1. Use the `DAYNAME()` function to determine the day of the week for each `submit_date`.
2. Categorize tasks into weekend submissions (Saturday and Sunday) and working day submissions (Monday through Friday).
3. Use conditional aggregation with `SUM()` to count the tasks in each category.

---

### Query

```sql
WITH cte AS (
    SELECT *,
           DAYNAME(submit_date) AS day_of_week
    FROM tasks
)

SELECT 
    SUM(CASE WHEN day_of_week = 'Saturday' OR day_of_week = 'Sunday' THEN 1 ELSE 0 END) AS weekend_cnt,
    SUM(CASE WHEN day_of_week != 'Saturday' AND day_of_week != 'Sunday' THEN 1 ELSE 0 END) AS working_cnt 
FROM cte;
