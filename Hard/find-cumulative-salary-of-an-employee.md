# Find Cumulative Salary of an Employee

You are given a table:

### Employee Table
| Column Name | Type |
|-------------|------|
| id          | int  |
| month       | int  |
| salary      | int  |

- `(id, month)` is the primary key (combination of columns with unique values) for this table.
- Each row in the table indicates the salary of an employee in one month during the year 2020.

### Task
Write a solution to calculate the **cumulative salary summary** for every employee in a single unified table. The cumulative salary summary for an employee can be calculated as follows:

1. For each month that the employee worked, sum up the salaries in that month and the previous two months. This is their **3-month sum** for that month.
2. If an employee did not work for the company in previous months, their effective salary for those months is `0`.
3. Do not include the 3-month sum for the **most recent month** that the employee worked in the summary.
4. Do not include the 3-month sum for any month the employee did not work.

### Result Requirements
- Return the result table ordered by `id` in ascending order. In case of a tie, order it by `month` in descending order.

### Input Example
#### Employee Table:
| id | month | salary |
|----|-------|--------|
| 1  | 1     | 20     |
| 2  | 1     | 20     |
| 1  | 2     | 30     |
| 2  | 2     | 30     |
| 3  | 2     | 40     |
| 1  | 3     | 40     |
| 3  | 3     | 60     |
| 1  | 4     | 60     |
| 3  | 4     | 70     |
| 1  | 7     | 90     |
| 1  | 8     | 90     |

### Output Example
| id | month | Salary |
|----|-------|--------|
| 1  | 7     | 90     |
| 1  | 4     | 130    |
| 1  | 3     | 90     |
| 1  | 2     | 50     |
| 1  | 1     | 20     |
| 2  | 1     | 20     |
| 3  | 3     | 100    |
| 3  | 2     | 40     |

### Explanation

1. **Employee 1**:
   - Exclude the most recent month (8).
   - Salaries excluding month 8:
     - **Month 7**: `90` (only 1 record, no previous months).
     - **Month 4**: `60 + 40 + 30 = 130`.
     - **Month 3**: `40 + 30 + 20 = 90`.
     - **Month 2**: `30 + 20 + 0 = 50`.
     - **Month 1**: `20 + 0 + 0 = 20`.

2. **Employee 2**:
   - Exclude the most recent month (2).
   - Salaries excluding month 2:
     - **Month 1**: `20 + 0 + 0 = 20`.

3. **Employee 3**:
   - Exclude the most recent month (4).
   - Salaries excluding month 4:
     - **Month 3**: `60 + 40 + 0 = 100`.
     - **Month 2**: `40 + 0 + 0 = 40`.

### Thought Process

1. **Identify the Most Recent Month**:
   - For each employee, find the most recent month they worked.

2. **Calculate Cumulative Salaries**:
   - Use a window function to calculate the 3-month rolling sum of salaries for each employee.
   - Include the current month and the previous two months in the sum.

3. **Exclude the Most Recent Month**:
   - Filter out rows corresponding to the most recent month for each employee.

4. **Ordering**:
   - Sort the results by `id` in ascending order.
   - For each employee, sort by `month` in descending order.

### Query
```sql
WITH cte AS (
    SELECT 
        id,
        month,
        SUM(salary) OVER (
            PARTITION BY id 
            ORDER BY month 
            RANGE BETWEEN 2 PRECEDING AND CURRENT ROW
        ) AS salary
    FROM 
        Employee
),
recent_months AS (
    SELECT 
        id, 
        MAX(month) AS max_month
    FROM 
        Employee
    GROUP BY 
        id
)

SELECT 
    c.id,
    c.month,
    c.salary
FROM 
    cte c
LEFT JOIN 
    recent_months r
ON 
    c.id = r.id
WHERE 
    c.month <> r.max_month
ORDER BY 
    c.id ASC, 
    c.month DESC;
