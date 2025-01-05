# Median Employee Salary

You are given a table:

### Employee Table
| Column Name | Type    |
|-------------|---------|
| id          | int     |
| company     | varchar |
| salary      | int     |

- `id` is the primary key (column with unique values) for this table.
- Each row indicates the company and the salary of one employee.

### Task
Write a solution to find the rows that contain the **median salary** of each company. While calculating the median, when you sort the salaries of the company, break the ties by `id`.

Return the result table in any order.

### Input Example
#### Employee Table:
| id | company | salary |
|----|---------|--------|
| 1  | A       | 2341   |
| 2  | A       | 341    |
| 3  | A       | 15     |
| 4  | A       | 15314  |
| 5  | A       | 451    |
| 6  | A       | 513    |
| 7  | B       | 15     |
| 8  | B       | 13     |
| 9  | B       | 1154   |
| 10 | B       | 1345   |
| 11 | B       | 1221   |
| 12 | B       | 234    |
| 13 | C       | 2345   |
| 14 | C       | 2645   |
| 15 | C       | 2645   |
| 16 | C       | 2652   |
| 17 | C       | 65     |

### Output Example
| id | company | salary |
|----|---------|--------|
| 5  | A       | 451    |
| 6  | A       | 513    |
| 12 | B       | 234    |
| 9  | B       | 1154   |
| 14 | C       | 2645   |

### Explanation

1. **Company A**:
   - Rows sorted by `salary` (breaking ties by `id`):
     | id | company | salary |
     |----|---------|--------|
     | 3  | A       | 15     |
     | 2  | A       | 341    |
     | 5  | A       | 451    | <-- median
     | 6  | A       | 513    | <-- median
     | 1  | A       | 2341   |
     | 4  | A       | 15314  |

2. **Company B**:
   - Rows sorted by `salary` (breaking ties by `id`):
     | id | company | salary |
     |----|---------|--------|
     | 8  | B       | 13     |
     | 7  | B       | 15     |
     | 12 | B       | 234    | <-- median
     | 11 | B       | 1221   | <-- median
     | 9  | B       | 1154   |
     | 10 | B       | 1345   |

3. **Company C**:
   - Rows sorted by `salary` (breaking ties by `id`):
     | id | company | salary |
     |----|---------|--------|
     | 17 | C       | 65     |
     | 13 | C       | 2345   |
     | 14 | C       | 2645   | <-- median
     | 15 | C       | 2645   |
     | 16 | C       | 2652   |

### Thought Process

1. **Sort Salaries Within Each Company**:
   - Use the `ROW_NUMBER()` or `RANK()` function to assign a rank to each row within a company, sorted by `salary` (breaking ties by `id`).

2. **Calculate Total Rows Per Company**:
   - Use the `COUNT()` function to determine the total number of rows (`cnt`) for each company.

3. **Identify Median Rows**:
   - If the total number of rows is odd, the median row is at `FLOOR(cnt / 2) + 1`.
   - If the total number of rows is even, the median rows are at positions `cnt / 2` and `cnt / 2 + 1`.

4. **Filter Rows**:
   - Use a `CASE` statement to select rows corresponding to the median positions.

### Query
```sql
WITH cte AS (
    SELECT 
        *, 
        ROW_NUMBER() OVER (PARTITION BY company ORDER BY salary, id) AS rk,
        COUNT(1) OVER (PARTITION BY company) AS cnt
    FROM 
        Employee
)

SELECT 
    id, 
    company, 
    salary
FROM 
    cte
WHERE 
    CASE 
        WHEN cnt % 2 = 0 THEN (rk = cnt / 2 OR rk = cnt / 2 + 1)
        ELSE (rk = FLOOR(cnt / 2) + 1)
    END;
