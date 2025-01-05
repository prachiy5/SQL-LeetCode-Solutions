# Average Salary: Departments VS Company

You are given two tables:

### Salary Table
| Column Name | Type |
|-------------|------|
| id          | int  |
| employee_id | int  |
| amount      | int  |
| pay_date    | date |

- `id` is the primary key column for this table.
- Each row of this table indicates the salary of an employee in one month.
- `employee_id` is a foreign key (reference column) from the Employee table.

### Employee Table
| Column Name   | Type |
|---------------|------|
| employee_id   | int  |
| department_id | int  |

- `employee_id` is the primary key column for this table.
- Each row of this table indicates the department of an employee.

### Task
Find the comparison result (**higher**, **lower**, or **same**) of the average salary of employees in a department to the company's average salary for each month.

### Result Requirements
- Return the result table in any order.

### Input Example
#### Salary Table:
| id | employee_id | amount | pay_date   |
|----|-------------|--------|------------|
| 1  | 1           | 9000   | 2017/03/31 |
| 2  | 2           | 6000   | 2017/03/31 |
| 3  | 3           | 10000  | 2017/03/31 |
| 4  | 1           | 7000   | 2017/02/28 |
| 5  | 2           | 6000   | 2017/02/28 |
| 6  | 3           | 8000   | 2017/02/28 |

#### Employee Table:
| employee_id | department_id |
|-------------|---------------|
| 1           | 1             |
| 2           | 2             |
| 3           | 2             |

### Output Example
| pay_month | department_id | comparison |
|-----------|---------------|------------|
| 2017-02   | 1             | same       |
| 2017-03   | 1             | higher     |
| 2017-02   | 2             | same       |
| 2017-03   | 2             | lower      |

### Explanation

1. **March 2017**:
   - Company's average salary = `(9000 + 6000 + 10000) / 3 = 8333.33`.
   - **Department 1**: Average salary = `9000`. Comparison: `higher`.
   - **Department 2**: Average salary = `(6000 + 10000) / 2 = 8000`. Comparison: `lower`.

2. **February 2017**:
   - Company's average salary = `(7000 + 6000 + 8000) / 3 = 7000`.
   - **Department 1**: Average salary = `7000`. Comparison: `same`.
   - **Department 2**: Average salary = `(6000 + 8000) / 2 = 7000`. Comparison: `same`.

### Thought Process

1. **Combine the Data**:
   - Use a join between the `Salary` and `Employee` tables to associate each salary with its respective department.

2. **Extract Pay Month**:
   - Convert the `pay_date` column to a `YYYY-MM` format to group salaries by month.

3. **Calculate Averages**:
   - Compute the **department average salary** for each month using a window function.
   - Compute the **company average salary** for each month.

4. **Compare Averages**:
   - Compare the department's average salary with the company's average salary for each month.

5. **Output the Result**:
   - Return the comparison results, including `pay_month`, `department_id`, and `comparison`.

### Query
```sql
WITH cte1 AS (
    SELECT 
        s.employee_id,
        s.amount,
        DATE_FORMAT(pay_date, '%Y-%m') AS pay_month,
        e.department_id
    FROM 
        salary s
    LEFT JOIN 
        employee e
    ON 
        s.employee_id = e.employee_id
),

cte2 AS (
    SELECT 
        *, 
        AVG(amount) OVER (PARTITION BY pay_month) AS department_avg
    FROM 
        cte1
),

cte3 AS (
    SELECT 
        pay_month,
        department_id,
        AVG(amount) AS avg_sal_employee,
        department_avg
    FROM 
        cte2
    GROUP BY 
        pay_month, department_id
)

SELECT 
    cte3.pay_month,
    cte3.department_id,
    CASE 
        WHEN avg_sal_employee > department_avg THEN 'higher'
        WHEN avg_sal_employee < department_avg THEN 'lower'
        ELSE 'same'
    END AS comparison
FROM 
    cte3;
