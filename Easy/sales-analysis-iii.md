# Sales Analysis III

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

- This table may contain duplicate rows.
- `product_id` is a foreign key referencing the `Product` table.
- Each row contains details about a sale, including the seller, product, buyer, date, quantity, and total price.

#### Task
Find the products that were **only sold in the first quarter of 2019** (i.e., between `2019-01-01` and `2019-03-31`, inclusive).

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

| product_id | product_name |
|------------|--------------|
| 1          | S8           |

**Explanation:**
- Product `1` was only sold in the first quarter of 2019.
- Product `2` was sold in the first quarter of 2019 but was also sold later.
- Product `3` was only sold after the first quarter of 2019.

---

### Thought Process

1. **Join Tables:**
   - Use an `INNER JOIN` to combine the `Product` and `Sales` tables based on `product_id`.

2. **Filter for Sales in Q1 2019:**
   - Identify products sold between `2019-01-01` and `2019-03-31`.

3. **Exclude Products Sold Outside Q1 2019:**
   - Use a subquery to find products with sales outside the specified date range.
   - Exclude these products from the result.

4. **Remove Duplicates:**
   - Use `DISTINCT` to ensure each product appears only once in the result.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT p.product_id, p.product_name, s.sale_date
  FROM Product p
  INNER JOIN Sales s
  ON p.product_id = s.product_id
)

SELECT DISTINCT product_id, product_name
FROM cte 
WHERE sale_date BETWEEN '2019-01-01' AND '2019-03-31'
  AND product_id NOT IN (
    SELECT product_id 
    FROM cte 
    WHERE sale_date NOT BETWEEN '2019-01-01' AND '2019-03-31'
  );
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Joins `Product` and `Sales` tables to retrieve `product_id`, `product_name`, and `sale_date` for each sale.

2. **Filter Sales in Q1 2019:**
   - Filters rows where `sale_date` is between `2019-01-01` and `2019-03-31`.

3. **Exclude Products Sold Outside Q1 2019:**
   - Uses a subquery to find `product_id` values with sales outside the specified date range.
   - Excludes these products from the main query using `NOT IN`.

4. **Remove Duplicates:**
   - Uses `DISTINCT` to ensure each `product_id` and `product_name` pair appears only once.

---

### Key Takeaways

- **Date Filtering:** Use `BETWEEN` for date range filtering.
- **Subqueries for Exclusion:** Use `NOT IN` to exclude rows based on a condition.
- **Joins for Data Integration:** Combine related data from multiple tables using `INNER JOIN`.

---

### Related Topics
- SQL Joins
- Filtering with Date Ranges
- Subqueries and Exclusion
- Common Table Expressions (CTEs)
