# Find the Best Seller by Total Sales Price

### Problem

#### Tables Overview

**Product**

| Column Name  | Type    |
|--------------|---------|
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |

- `product_id` is the primary key.
- Each row indicates the name and price of a product.

**Sales**

| Column Name | Type |
|-------------|------|
| seller_id   | int  |
| product_id  | int  |
| buyer_id    | int  |
| sale_date   | date |
| quantity    | int  |
| price       | int  |

- This table may contain repeated rows.
- `product_id` is a foreign key referencing the `Product` table.
- Each row contains information about a single sale, including the seller, product, buyer, date, quantity sold, and total sale price.

#### Task
Write a query to find the seller(s) with the highest total sales price.
- If there is a tie, include all tied sellers in the result.

---

### Example

**Input:**

`Product` table:

| product_id | product_name | unit_price |
|------------|--------------|------------|
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |

`Sales` table:

| seller_id | product_id | buyer_id | sale_date  | quantity | price |
|-----------|------------|----------|------------|----------|-------|
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |

**Output:**

| seller_id |
|-----------|
| 1         |
| 3         |

**Explanation:**
- Seller `1` has total sales of `2000 + 800 = 2800`.
- Seller `2` has total sales of `800`.
- Seller `3` has total sales of `2800`.
- Sellers `1` and `3` have the highest total sales, so both are included in the output.

---

### Thought Process

1. **Understand the Relationships:**
   - Use `INNER JOIN` to link the `Product` and `Sales` tables using `product_id`.

2. **Calculate Total Sales by Seller:**
   - Group rows by `seller_id`.
   - Use `SUM(price)` to calculate the total sales for each seller.

3. **Rank Sellers by Total Sales:**
   - Use the `RANK()` window function to rank sellers based on their total sales in descending order.

4. **Filter Top Sellers:**
   - Select rows where the rank (`r`) equals `1` to get the best seller(s).

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte1 AS (
  SELECT s.seller_id, 
         SUM(s.price) AS total_sales, 
         RANK() OVER (ORDER BY SUM(s.price) DESC) AS r
  FROM Product p
  INNER JOIN Sales s
  ON p.product_id = s.product_id
  GROUP BY s.seller_id
)

SELECT seller_id
FROM cte1
WHERE r = 1;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Joins the `Product` and `Sales` tables on `product_id`.
   - Groups data by `seller_id` and calculates the total sales for each seller using `SUM(price)`.
   - Assigns a rank (`r`) to each seller based on their total sales in descending order using `RANK()`.

2. **Final Query:**
   - Selects `seller_id` from the CTE where the rank (`r`) equals `1`.
   - This ensures only the top sellers (with the highest total sales) are included in the result.

---

### Key Takeaways

- **Aggregate Functions:** Use `SUM()` to calculate total sales.
- **Window Functions:** Use `RANK()` to rank groups based on aggregate values.
- **Efficient Filtering:** Use a `WITH` clause (CTE) to simplify complex queries and filter results easily.

---

### Related Topics
- SQL Window Functions
- Aggregate Functions (SUM, COUNT)
- GROUP BY and HAVING
