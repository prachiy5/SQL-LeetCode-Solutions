
# The Number of Seniors and Juniors to Join the Company

### Table: Candidates

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
- Each row of this table indicates the id of a candidate, their monthly salary, and their experience.

### Problem Description

A company wants to hire new employees. The budget of the company for the salaries is **$70000**. The company's criteria for hiring are:

1. Hire the **largest number of seniors**.
2. After hiring the maximum number of seniors, use the **remaining budget** to hire the largest number of juniors.

### Expected Output

Write a solution to find the number of seniors and juniors hired under the mentioned criteria.

The output table should be:

```
+------------+---------------------+
| experience | accepted_candidates |
+------------+---------------------+
```
Where:
- `experience` represents the experience level ('Senior' or 'Junior').
- `accepted_candidates` represents the number of candidates hired at that experience level.

### Example 1

#### Input:

**Candidates table:**

```
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 10000  |
| 2           | Senior     | 20000  |
| 11          | Senior     | 20000  |
| 13          | Senior     | 50000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
```

#### Output:

```
+------------+---------------------+
| experience | accepted_candidates |
+------------+---------------------+
| Senior     | 2                   |
| Junior     | 2                   |
+------------+---------------------+
```

#### Explanation:
- We can hire 2 seniors with IDs (2, 11). Their total salary is $40,000, leaving $30,000 in the budget.
- Using the remaining budget, we can hire 2 juniors with IDs (1, 9). Their total salary is $20,000, leaving $10,000 unused.
- We cannot hire the senior with ID 13 or the junior with ID 4 due to budget constraints.

### Example 2

#### Input:

**Candidates table:**

```
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 10000  |
| 2           | Senior     | 80000  |
| 11          | Senior     | 80000  |
| 13          | Senior     | 80000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
```

#### Output:

```
+------------+---------------------+
| experience | accepted_candidates |
+------------+---------------------+
| Senior     | 0                   |
| Junior     | 3                   |
+------------+---------------------+
```

#### Explanation:
- We cannot hire any seniors as their salaries exceed the budget.
- We can hire all 3 juniors with IDs (1, 9, 4). Their total salary is $30,000, leaving $10,000 unused.

---

### Thought Process

1. **Separate Senior and Junior Candidates**: First, filter out the candidates based on their experience levels.
2. **Hire Seniors First**:
   - Sort seniors by their salaries in ascending order.
   - Use a running total to track the cumulative cost of hiring seniors within the $70,000 budget.
3. **Calculate Remaining Budget**:
   - Subtract the total cost of hiring seniors from the $70,000 budget.
4. **Hire Juniors Next**:
   - Sort juniors by their salaries in ascending order.
   - Use the remaining budget to hire as many juniors as possible.
5. **Count Accepted Candidates**: Count the number of seniors and juniors hired.

---

### SQL Query

```sql
WITH seniors AS (
    SELECT employee_id, experience, salary,
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
    SELECT CASE
    WHEN SUM(salary) IS NULL THEN 70000
    ELSE 70000 - SUM(salary) 
    END AS remaining_budget
    FROM selected_seniors
),

juniors AS (
    SELECT employee_id, experience, salary,
           SUM(salary) OVER (ORDER BY salary) AS running_total
    FROM candidates
    WHERE experience = 'Junior'
),
selected_juniors AS (
    SELECT j.*
    FROM juniors j, remaining_budget rb
    WHERE j.running_total <= rb.remaining_budget
)

SELECT 'Senior' AS experience, COUNT(*) AS accepted_candidates
FROM selected_seniors
UNION ALL
SELECT 'Junior' AS experience, COUNT(*) AS accepted_candidates
FROM selected_juniors;
