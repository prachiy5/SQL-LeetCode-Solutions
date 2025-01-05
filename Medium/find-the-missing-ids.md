### Find the Missing IDs
**Table: Customers**
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| customer_name | varchar |
+---------------+---------+
```
- `customer_id` is the column with unique values for this table.
- Each row of this table contains the name and the ID of a customer.

Write a solution to find the missing customer IDs. The missing IDs are ones that are not in the `Customers` table but are in the range between 1 and the maximum `customer_id` present in the table.

Notice that the maximum `customer_id` will not exceed 100.

Return the result table ordered by IDs in ascending order.

### Example

#### Input

**Customers table:**

| customer_id | customer_name |
|-------------|---------------|
| 1           | Alice         |
| 4           | Bob           |
| 5           | Charlie       |

#### Output

| ids |
|-----|
| 2   |
| 3   |

#### Explanation

The maximum `customer_id` present in the table is 5, so in the range [1, 5], IDs 2 and 3 are missing from the table.

---

### Thought Process

1. Use a recursive Common Table Expression (CTE) to generate all numbers from 1 to the maximum `customer_id` in the `Customers` table.
2. Compare this range of numbers with the `customer_id` values present in the `Customers` table.
3. Identify the numbers that are in the range but not in the table.
4. Return these missing numbers ordered by `ids` in ascending order.

---

### SQL Query

```sql
WITH RECURSIVE recursive_num_range AS (
    SELECT 1 AS customer_id
    UNION ALL
    SELECT customer_id + 1
    FROM recursive_num_range
    WHERE customer_id < (SELECT MAX(customer_id) FROM customers)
)

SELECT customer_id AS ids
FROM recursive_num_range
WHERE customer_id NOT IN (SELECT customer_id FROM customers)
ORDER BY customer_id;
