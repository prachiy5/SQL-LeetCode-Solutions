# Product Sales Analysis-II
### Problem

#### Tables Overview

**Sales**

| Column Name | Type |
|-------------|------|
| sale_id     | int  |
| product_id  | int  |
| year        | int  |
| quantity    | int  |
| price       | int  |

- `(sale_id, year)` is the primary key.
- `product_id` is a foreign key referencing the `product_id` column in the `Product` table.
- Each row shows a sale of a product in a specific year with its price per unit and quantity.

**Product**

| Column Name  | Type    |
|--------------|---------|
| product_id   | int     |
| product_name | varchar |

- `product_id` is the primary key.
- Each row indicates the name of a product.

#### Task
Write a query to report the `product_name`, `year`, and `price` for each sale in the `Sales` table.

---

### Example

**Input:**

`Sales` table:

| sale_id | product_id | year | quantity | price |
|---------|------------|------|----------|-------|
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |

`Product` table:

| product_id | product_name |
|------------|--------------|
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |

**Output:**

| product_name | year  | price |
|--------------|-------|-------|
| Nokia        | 2008  | 5000  |
| Nokia        | 2009  | 5000  |
| Apple        | 2011  | 9000  |

**Explanation:**
- For sale_id = 1, product_id = 100 corresponds to Nokia, sold in 2008 for 5000.
- For sale_id = 2, product_id = 100 corresponds to Nokia, sold in 2009 for 5000.
- For sale_id = 7, product_id = 200 corresponds to Apple, sold in 2011 for 9000.

---

### Thought Process

1. **Understand the Relationship Between Tables:**
   - `Sales` table contains the details of each sale, including the product and its price.
   - `Product` table contains the product names.
   - Use the `product_id` column to join these tables.

2. **Write the Query:**
   - Perform an `INNER JOIN` between the `Sales` and `Product` tables using `product_id`.
   - Select `product_name`, `year`, and `price` from the resulting joined table.

3. **Return Result:**
   - Ensure the output contains the correct columns in the required order.
   - The result does not require any specific sorting.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT p.product_name, s.year, s.price 
FROM Product p
INNER JOIN Sales s
ON p.product_id = s.product_id;
```

---

### Solution Explanation

1. **INNER JOIN:**
   - Joins the `Product` table (`p`) with the `Sales` table (`s`) using the `product_id` column.
   - Ensures only matching rows (products with sales) are included in the result.

2. **SELECT Statement:**
   - Retrieves `product_name` from `Product` and `year`, `price` from `Sales`.

3. **Result:**
   - The result includes the product name, the year of the sale, and the price for each sale.

---

### Key Takeaways

- **INNER JOIN:** Use it to combine rows from two tables based on matching values in a related column.
- **Result Columns:** Carefully select the required columns from the joined tables.
- **Foreign Key Relationship:** Leverage the relationship between tables to retrieve related data.

---

### Related Topics
- SQL Joins
- Foreign Keys
- Selecting Columns from Multiple Tables
