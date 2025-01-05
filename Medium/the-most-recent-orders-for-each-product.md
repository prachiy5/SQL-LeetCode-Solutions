### The Most Recent Orders for Each Product

#### Tables:

**Customers Table:**

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| name          | varchar |

- `customer_id` is the column with unique values for this table.
- This table contains information about the customers.

**Orders Table:**

| Column Name   | Type    |
|---------------|---------|
| order_id      | int     |
| order_date    | date    |
| customer_id   | int     |
| product_id    | int     |

- `order_id` is the column with unique values for this table.
- This table contains information about the orders made by `customer_id`.
- There will be no product ordered by the same user more than once in one day.

**Products Table:**

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| product_name  | varchar |
| price         | int     |

- `product_id` is the column with unique values for this table.
- This table contains information about the products.

#### Task:
Write a solution to find the most recent order(s) of each product. 

- Return the result table ordered by `product_name` in ascending order.
- In case of a tie, order them by `product_id` in ascending order.
- If there is still a tie, order them by `order_id` in ascending order.

#### Output Format:

The result table should contain:

| product_name | product_id | order_id | order_date |
|--------------|------------|----------|------------|

### Example Input:

**Customers Table:**

| customer_id | name      |
|-------------|-----------|
| 1           | Winston   |
| 2           | Jonathan  |
| 3           | Annabelle |
| 4           | Marwan    |
| 5           | Khaled    |

**Orders Table:**

| order_id | order_date | customer_id | product_id |
|----------|------------|-------------|------------|
| 1        | 2020-07-31 | 1           | 1          |
| 2        | 2020-07-30 | 2           | 2          |
| 3        | 2020-08-29 | 3           | 3          |
| 4        | 2020-07-29 | 4           | 1          |
| 5        | 2020-06-10 | 1           | 2          |
| 6        | 2020-08-01 | 2           | 1          |
| 7        | 2020-08-01 | 3           | 1          |
| 8        | 2020-08-03 | 1           | 2          |
| 9        | 2020-08-07 | 2           | 3          |
| 10       | 2020-07-15 | 1           | 2          |

**Products Table:**

| product_id | product_name | price |
|------------|--------------|-------|
| 1          | keyboard     | 120   |
| 2          | mouse        | 80    |
| 3          | screen       | 600   |
| 4          | hard disk    | 450   |

### Example Output:

| product_name | product_id | order_id | order_date |
|--------------|------------|----------|------------|
| keyboard     | 1          | 6        | 2020-08-01 |
| keyboard     | 1          | 7        | 2020-08-01 |
| mouse        | 2          | 8        | 2020-08-03 |
| screen       | 3          | 3        | 2020-08-29 |

### Explanation:
1. **keyboard:** Most recent orders are on `2020-08-01`, with two orders (`order_id` 6 and 7).
2. **mouse:** Most recent order is on `2020-08-03`, with `order_id` 8.
3. **screen:** Most recent order is on `2020-08-29`, with `order_id` 3.
4. **hard disk:** No orders exist for this product, so it is not included in the result.

---

### Thought Process:
1. Use a Common Table Expression (CTE) to join the `Products` and `Orders` tables to combine product information with order details.
2. Use the `DENSE_RANK()` window function to rank orders for each product by `order_date` in descending order.
3. Filter for only the most recent orders (i.e., rank = 1).
4. Order the final result by `product_name`, `product_id`, and `order_id` as required.

---

### Query:
```sql
WITH cte AS (
    SELECT product_name, p.product_id, order_id, order_date,
           DENSE_RANK() OVER (PARTITION BY product_id ORDER BY order_date DESC) AS r
    FROM products p 
    INNER JOIN orders o
    ON p.product_id = o.product_id
)

SELECT product_name, product_id, order_id, order_date
FROM cte 
WHERE r = 1
ORDER BY product_name, product_id, order_id;


