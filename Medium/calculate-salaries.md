### Calculate Salaries
We are given a table:

#### Table: `Salaries`

| Column Name   | Type    |
|---------------|---------|
| company_id    | int     |
| employee_id   | int     |
| employee_name | varchar |
| salary        | int     |

- `(company_id, employee_id)` is the primary key.
- Each row contains information about an employee's salary and the company they work for.

### Task
Write an SQL query to calculate the salary of each employee after applying taxes. The tax rate for each company is determined by the maximum salary in that company:

1. **Tax Rate:**
   - **0%** if the maximum salary in the company is less than $1000.
   - **24%** if the maximum salary is between $1000 and $10000 (inclusive).
   - **49%** if the maximum salary exceeds $10000.

2. The final salary is calculated using the formula:
   
   \[
   \text{final salary} = \text{salary} - \left(\frac{\text{tax rate}}{100} \times \text{salary}\right)
   \]

3. The salary should be rounded to the nearest integer.

4. Return the result ordered by `company_id`.

---

### Example

#### Input:
**Salaries table:**

| company_id | employee_id | employee_name | salary |
|------------|-------------|---------------|--------|
| 1          | 1           | Tony          | 2000   |
| 1          | 2           | Pronub        | 21300  |
| 1          | 3           | Tyrrox        | 10800  |
| 2          | 1           | Pam           | 300    |
| 2          | 7           | Bassem        | 450    |
| 2          | 9           | Hermione      | 700    |
| 3          | 7           | Bocaben       | 100    |
| 3          | 2           | Ognjen        | 2200   |
| 3          | 13          | Nyancat       | 3300   |
| 3          | 15          | Morninngcat   | 7777   |

#### Output:

| company_id | employee_id | employee_name | salary |
|------------|-------------|---------------|--------|
| 1          | 1           | Tony          | 1020   |
| 1          | 2           | Pronub        | 10863  |
| 1          | 3           | Tyrrox        | 5508   |
| 2          | 1           | Pam           | 300    |
| 2          | 7           | Bassem        | 450    |
| 2          | 9           | Hermione      | 700    |
| 3          | 7           | Bocaben       | 76     |
| 3          | 2           | Ognjen        | 1672   |
| 3          | 13          | Nyancat       | 2508   |
| 3          | 15          | Morninngcat   | 5911   |

#### Explanation:
- **Company 1:** Maximum salary = $21300. Tax rate = 49%.
  - Tony: $2000 - $2000 * 0.49 = $1020.
  - Pronub: $21300 - $21300 * 0.49 = $10863.
  - Tyrrox: $10800 - $10800 * 0.49 = $5508.

- **Company 2:** Maximum salary = $700. Tax rate = 0%.
  - Pam, Bassem, and Hermione retain their full salaries.

- **Company 3:** Maximum salary = $7777. Tax rate = 24%.
  - Bocaben: $100 - $100 * 0.24 = $76.
  - Ognjen: $2200 - $2200 * 0.24 = $1672.
  - Nyancat: $3300 - $3300 * 0.24 = $2508.
  - Morninngcat: $7777 - $7777 * 0.24 = $5911.

---

### Thought Process
1. **Determine Maximum Salary Per Company:**
   - Use a window function to calculate the maximum salary within each company.

2. **Calculate Tax Rates:**
   - Based on the maximum salary, assign tax rates using a `CASE` statement.

3. **Apply Taxes to Salaries:**
   - Use the formula to adjust salaries based on the determined tax rates.

4. **Return Results:**
   - Select the required fields and order the results by `company_id`.

---

### SQL Query
```sql
WITH cte AS (
    SELECT *,
           MAX(salary) OVER (PARTITION BY company_id) AS max_company_sal
    FROM salaries
)

SELECT 
    company_id,
    employee_id,
    employee_name,
    CASE 
        WHEN max_company_sal < 1000 THEN salary
        WHEN max_company_sal BETWEEN 1000 AND 10000 THEN ROUND(salary - (salary * 0.24), 0)
        WHEN max_company_sal > 10000 THEN ROUND(salary - (salary * 0.49), 0)
    END AS salary
FROM cte
ORDER BY company_id;
```

---

### Key Points
- **Window Functions:** Used `MAX` with `OVER (PARTITION BY)` to find the maximum salary per company.
- **Conditional Logic:** `CASE` statement to determine the tax rate.
- **Rounding:** Used `ROUND` to ensure the final salary is a whole number.
- **Output Ordering:** Ensured the output is ordered by `company_id` for clarity.
