### Product Sales Analysis IV

We are given two tables:

#### Table: Sales
| Column Name | Type  |
|-------------|-------|
| sale_id     | int   |
| product_id  | int   |
| user_id     | int   |
| quantity    | int   |

- `sale_id` contains unique values.
- `product_id` is a foreign key referencing the `Product` table.
- Each row shows the ID of the product and the quantity purchased by a user.

#### Table: Product
| Column Name | Type |
|-------------|------|
| product_id  | int  |
| price       | int  |

- `product_id` contains unique values.
- Each row indicates the price of each product.

#### Task
Write a query to report, for each user, the product ID on which the user spent the most money. If the same user spent the most money on multiple products, include all of them.

The result can be returned in any order.

#### Example Input/Output

##### Input:
**Sales table:**
| sale_id | product_id | user_id | quantity |
|---------|------------|---------|----------|
| 1       | 1          | 101     | 10       |
| 2       | 3          | 101     | 7        |
| 3       | 1          | 102     | 9        |
| 4       | 2          | 102     | 6        |
| 5       | 3          | 102     | 10       |
| 6       | 1          | 102     | 6        |

**Product table:**
| product_id | price |
|------------|-------|
| 1          | 10    |
| 2          | 25    |
| 3          | 15    |

##### Output:
| user_id | product_id |
|---------|------------|
| 101     | 3          |
| 102     | 1          |
| 102     | 2          |
| 102     | 3          |

##### Explanation:
- **User 101:**
  - Spent `10 * 10 = 100` on product 1.
  - Spent `7 * 15 = 105` on product 3.
  - Maximum spending is on product 3.

- **User 102:**
  - Spent `(9 + 6) * 10 = 150` on product 1.
  - Spent `6 * 25 = 150` on product 2.
  - Spent `10 * 15 = 150` on product 3.
  - Maximum spending is tied among products 1, 2, and 3.

---

### Thought Process
1. Join the `Sales` table with the `Product` table on `product_id` to get the `price` for each product.
2. Calculate the total amount spent for each `user_id` and `product_id` by multiplying `quantity` with `price` and summing it up.
3. Use a Common Table Expression (CTE) to calculate the total spending for each combination of `user_id` and `product_id`.
4. Rank the products for each user by their total spending using `DENSE_RANK()` in descending order. If multiple products have the same total spending, they will have the same rank.
5. Select the products with the highest rank (rank = 1) for each user.

---

### Query
```sql
WITH cte AS (
    SELECT s.product_id, user_id, 
           SUM(quantity) * price AS total_price
    FROM sales s
    INNER JOIN product p
    ON s.product_id = p.product_id
    GROUP BY product_id, user_id
),

cte2 AS (
    SELECT product_id, user_id, total_price,
           DENSE_RANK() OVER (PARTITION BY user_id ORDER BY total_price DESC) AS rn 
    FROM cte
)

SELECT product_id, user_id
FROM cte2
WHERE rn = 1;
```

---

### Key Takeaways
- **Joins:** Joining tables on a common key allows us to enrich the data by combining columns from different tables.
- **Aggregations:** Using `SUM()` helps calculate the total amount spent by a user on each product.
- **Window Functions:** `DENSE_RANK()` ensures tied values are given the same rank, which is essential for handling ties in spending.
- **CTEs:** Common Table Expressions help break down the problem into manageable steps, making the query easier to read and maintain.

---

### Related Topics
- SQL Joins
- Aggregate Functions
- Window Functions (e.g., `DENSE_RANK()`, `ROW_NUMBER()`)
- Common Table Expressions (CTEs)
