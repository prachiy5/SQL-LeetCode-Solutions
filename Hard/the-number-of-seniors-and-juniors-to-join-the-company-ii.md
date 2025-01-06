#### The Number of Seniors and Juniors to Join the Company II

A company wants to hire new employees with a budget of $70,000 for salaries. The hiring criteria are:

1. Keep hiring the senior candidate with the smallest salary until the budget is insufficient to hire more seniors.
2. Use the remaining budget to hire junior candidates, starting with the one with the smallest salary.
3. Continue hiring juniors until the remaining budget is insufficient.

Return the IDs of the seniors and juniors hired based on this hiring strategy. The salary of each candidate is guaranteed to be unique.

**Schema**

**Table: Candidates**

```
+-------------+------+ 
| Column Name | Type |
+-------------+------+ 
| employee_id | int  | 
| experience  | enum | 
| salary      | int  | 
+-------------+------+ 
```

- `employee_id` is the column with unique values for this table.
- `experience` is an ENUM (category) type with values ('Senior', 'Junior').
- `salary` is the monthly salary of the candidate.

**Example Input 1:**

```
Candidates table:
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 15000  |
| 2           | Senior     | 20000  |
| 11          | Senior     | 16000  |
| 13          | Senior     | 50000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
```

**Output:**

```
+-------------+
| employee_id |
+-------------+
| 11          |
| 2           |
| 1           |
| 9           |
+-------------+
```

**Explanation:**

1. Hire seniors with IDs (11, 2). Their combined salaries are $36,000. The remaining budget is $34,000.
2. Hire juniors with IDs (1, 9). Their combined salaries are $25,000. The remaining budget is $9,000, which is insufficient to hire the junior candidate with ID 4.

---

**Thought Process:**

1. Separate the candidates into seniors and juniors.
2. For seniors, calculate a running total of their salaries ordered by ascending salary. Select seniors whose cumulative salaries fit within the $70,000 budget.
3. Calculate the remaining budget after hiring seniors.
4. For juniors, calculate a running total of their salaries ordered by ascending salary. Select juniors whose cumulative salaries fit within the remaining budget.
5. Combine the selected senior and junior IDs and return the result.

---

**SQL Query:**

```sql
WITH seniors AS (
    SELECT *,
           SUM(salary) OVER (ORDER BY salary) AS running_total
    FROM candidates
    WHERE experience = 'Senior'
),

selected_seniors AS (
    SELECT * 
    FROM seniors
    WHERE running_total <= 70000
),

remaining_budget AS (
    SELECT 
        CASE 
            WHEN SUM(salary) IS NULL THEN 70000
            ELSE 70000 - SUM(salary)
        END AS remaining_budget
    FROM selected_seniors
),

juniors AS (
    SELECT *,
           SUM(salary) OVER (ORDER BY salary) AS running_total
    FROM candidates
    WHERE experience = 'Junior'
),

selected_juniors AS (
    SELECT * 
    FROM juniors, remaining_budget
    WHERE running_total <= remaining_budget
)

SELECT employee_id 
FROM selected_seniors
UNION 
SELECT employee_id 
FROM selected_juniors;
