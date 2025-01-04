# Sales Analysis II
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

- This table might have repeated rows.
- `product_id` is a foreign key referencing the `Product` table.
- `buyer_id` and `sale_date` are guaranteed not to be `NULL`.
- Each row contains information about one sale, including the seller, product, buyer, date, quantity, and total price.

#### Task
Write a query to find the buyers who purchased an **S8** but did not purchase an **iPhone**. Both `S8` and `iPhone` are products in the `Product` table.

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
| 2         | 1          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 3        | 2019-05-13 | 2        | 2800  |

**Output:**

| buyer_id |
|----------|
| 1        |

**Explanation:**
- Buyer `1` bought an `S8` but not an `iPhone`.
- Buyer `3` bought both `S8` and `iPhone`, so they are excluded.

---

### Thought Process

1. **Join Tables:**
   - Use an `INNER JOIN` to combine `Product` and `Sales` tables based on `product_id`.
   - Retrieve the `product_name`, `buyer_id`, and other relevant columns.

2. **Identify Buyers of S8:**
   - Filter rows where `product_name = 'S8'`.

3. **Exclude Buyers of iPhone:**
   - Use a subquery to identify buyers who purchased `iPhone`.
   - Exclude these buyers from the list of `S8` buyers using `NOT IN`.

4. **Remove Duplicates:**
   - Use `DISTINCT` to ensure each buyer ID appears only once in the result.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT p.product_id, p.product_name, s.buyer_id, s.price 
  FROM Product p
  INNER JOIN Sales s
  ON p.product_id = s.product_id
)

SELECT DISTINCT buyer_id 
FROM cte 
WHERE product_name = 'S8' 
  AND buyer_id NOT IN (
    SELECT buyer_id 
    FROM cte 
    WHERE product_name = 'iPhone'
  );
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Joins `Product` and `Sales` tables to retrieve relevant data, including `product_name` and `buyer_id`.

2. **Filter for S8 Buyers:**
   - Filters rows where `product_name = 'S8'`.

3. **Exclude iPhone Buyers:**
   - Uses a subquery to find buyers who purchased `iPhone`.
   - Excludes these buyers from the main query using `NOT IN`.

4. **Remove Duplicates:**
   - Uses `DISTINCT` to ensure each `buyer_id` appears only once in the result.

---

### Key Takeaways

- **Subqueries for Exclusion:** Use `NOT IN` to exclude specific rows based on another query.
- **Joins for Data Combination:** Combine related data from multiple tables using `INNER JOIN`.
- **Common Table Expressions:** Simplify complex queries by breaking them into smaller, manageable parts.

---

### Related Topics
- SQL Joins
- Subqueries for Filtering
- Common Table Expressions (CTEs)
