### The Most Frequently Ordered Products for Each Customer

You are tasked with finding the most frequently ordered product(s) for each customer who has made at least one order. You are provided with three tables:

1. **Customers Table**: Contains customer IDs and names.
2. **Orders Table**: Contains order details, including the product ID and customer ID.
3. **Products Table**: Contains product details such as product ID and product name.

For each customer who has made at least one order, determine the product(s) they ordered the most frequently. If a customer has multiple products tied for the highest order frequency, include all such products in the result.

Return the result table ordered by `customer_id` and include the columns `customer_id`, `product_id`, and `product_name`.

#### Input Data

**Customers Table**:
| customer_id | name      |
|-------------|-----------|
| 1           | Alice     |
| 2           | Bob       |
| 3           | Tom       |
| 4           | Jerry     |
| 5           | John      |

**Orders Table**:
| order_id | order_date | customer_id | product_id |
|----------|------------|-------------|------------|
| 1        | 2020-07-31 | 1           | 1          |
| 2        | 2020-07-30 | 2           | 2          |
| 3        | 2020-08-29 | 3           | 3          |
| 4        | 2020-07-29 | 4           | 1          |
| 5        | 2020-06-10 | 1           | 2          |
| 6        | 2020-08-01 | 2           | 1          |
| 7        | 2020-08-01 | 3           | 3          |
| 8        | 2020-08-03 | 1           | 2          |
| 9        | 2020-08-07 | 2           | 3          |
| 10       | 2020-07-15 | 1           | 2          |

**Products Table**:
| product_id | product_name | price |
|------------|--------------|-------|
| 1          | keyboard     | 120   |
| 2          | mouse        | 80    |
| 3          | screen       | 600   |
| 4          | hard disk    | 450   |

#### Output

| customer_id | product_id | product_name |
|-------------|------------|--------------|
| 1           | 2          | mouse        |
| 2           | 1          | keyboard     |
| 2           | 2          | mouse        |
| 2           | 3          | screen       |
| 3           | 3          | screen       |
| 4           | 1          | keyboard     |

---

### Thought Process

1. **Join Tables**:
   - Join the `Customers` table with the `Orders` table to get the orders made by each customer.
   - Join the resulting table with the `Products` table to include product details for the ordered products.

2. **Count Orders**:
   - Group the data by `customer_id`, `product_id`, and `product_name` to calculate the number of times each product was ordered by each customer.

3. **Determine Most Frequent Orders**:
   - Use a window function (`DENSE_RANK`) partitioned by `customer_id` and ordered by the count of orders in descending order to identify the most frequently ordered products for each customer.

4. **Filter Top Results**:
   - Select only the rows where the rank is `1`, indicating the most frequently ordered product(s) for each customer.

5. **Output Requirements**:
   - Return the `customer_id`, `product_id`, and `product_name`.
   - Ensure the result is ordered by `customer_id`.

---

### Query

```sql
WITH cte AS (
    SELECT 
        c.customer_id,
        p.product_id,
        p.product_name,
        COUNT(o.order_id) AS num_orders
    FROM customers c
    INNER JOIN orders o
        ON c.customer_id = o.customer_id
    INNER JOIN products p
        ON o.product_id = p.product_id
    GROUP BY c.customer_id, p.product_id, p.product_name
),

cte2 AS (
    SELECT 
        customer_id,
        product_id,
        product_name, 
        DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY num_orders DESC) AS r
    FROM cte
)

SELECT 
    customer_id, 
    product_id, 
    product_name
FROM cte2
WHERE r = 1;
```

---

### Explanation of the Query

1. **First CTE (`cte`)**:
   - Joins the `Customers`, `Orders`, and `Products` tables to link customers with the products they ordered.
   - Groups the data by `customer_id`, `product_id`, and `product_name`.
   - Calculates the total number of orders (`num_orders`) for each product ordered by each customer.

2. **Second CTE (`cte2`)**:
   - Applies the `DENSE_RANK` window function to rank products for each customer based on the number of orders (`num_orders`) in descending order.
   - Assigns a rank of `1` to the most frequently ordered product(s) for each customer.

3. **Final Selection**:
   - Filters rows where the rank (`r`) is `1`, ensuring only the most frequently ordered product(s) for each customer are included.

4. **Result Ordering**:
   - The query ensures the output is ordered by `customer_id` as per the problem requirements.



---
