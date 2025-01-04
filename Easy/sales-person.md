# Sales person
### Problem

#### Tables Overview

**SalesPerson**

| Column Name     | Type    |
|-----------------|---------|
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |

- `sales_id` is the primary key.
- Each row contains information about a salesperson, including their name, salary, commission rate, and hire date.

**Company**

| Column Name | Type    |
|-------------|---------|
| com_id      | int     |
| name        | varchar |
| city        | varchar |

- `com_id` is the primary key.
- Each row contains information about a company, including its name and city.

**Orders**

| Column Name | Type |
|-------------|------|
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |

- `order_id` is the primary key.
- `com_id` is a foreign key referencing the `com_id` column in the `Company` table.
- `sales_id` is a foreign key referencing the `sales_id` column in the `SalesPerson` table.

#### Task
Find the names of all salespersons who did **not** have any orders related to the company with the name "RED".

Return the result table in any order.

---

### Example

**Input:**

`SalesPerson` table:

| sales_id | name | salary | commission_rate | hire_date  |
|----------|------|--------|-----------------|------------|
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |

`Company` table:

| com_id | name   | city     |
|--------|--------|----------|
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |

`Orders` table:

| order_id | order_date | com_id | sales_id | amount |
|----------|------------|--------|----------|--------|
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |

**Output:**

| name |
|------|
| Amy  |
| Mark |
| Alex |

**Explanation:**
- John and Pam are the only salespersons who have orders related to company RED.
- Therefore, Amy, Mark, and Alex are returned as they do not have any orders for RED.

---

### Thought Process

1. **Understand the Relationships:**
   - Use the `Orders` table to find all salespersons (`sales_id`) with orders related to company RED.
   - Exclude these salespersons from the list of all salespersons in the `SalesPerson` table.

2. **Join Tables Appropriately:**
   - Join the `Orders` table with the `Company` table to filter orders for company RED.
   - Use a `LEFT JOIN` or a `NOT IN` subquery to exclude salespersons with orders for RED.

3. **Handle Edge Cases:**
   - Ensure that salespersons with no orders at all are included in the result.

4. **Select Relevant Data:**
   - Return the names of the salespersons who meet the condition.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT DISTINCT sp.name
FROM Orders o
RIGHT JOIN SalesPerson sp
    ON sp.sales_id = o.sales_id
LEFT JOIN Company c
    ON c.com_id = o.com_id
WHERE c.name IS NULL 
   OR sp.sales_id NOT IN (
        SELECT sales_id
        FROM Orders o
        LEFT JOIN Company c
            ON o.com_id = c.com_id
        WHERE c.name = 'RED'
    );
```

---

### Solution Explanation

1. **Subquery for RED Orders:**
   - The subquery retrieves all `sales_id` values for salespersons who have orders for company RED.

2. **Main Query:**
   - Starts with all salespersons (`SalesPerson` table).
   - Filters out salespersons who are in the subquery result (those with orders for RED).
   - Also includes salespersons with no orders at all (`c.name IS NULL`).

3. **JOIN Operations:**
   - `RIGHT JOIN` ensures all salespersons are included, even if they have no orders.
   - `LEFT JOIN` with `Company` filters out orders unrelated to RED.

4. **DISTINCT:**
   - Ensures each salesperson name is listed only once.

---

### Key Takeaways

- **JOIN Variations:** Use `LEFT JOIN` and `RIGHT JOIN` strategically to include or exclude rows.
- **Subqueries:** Efficient for filtering specific groups of data.
- **DISTINCT:** Ensures unique results in the final output.

---

### Related Topics
- SQL Joins
- Filtering Rows with Subqueries
- Using NOT IN and NULL Handling
