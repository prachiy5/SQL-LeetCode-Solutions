# Products with Large Orders in February 2020

### Problem

#### Table: Products

| Column Name      | Type    |
|------------------|---------|
| product_id       | int     |
| product_name     | varchar |
| product_category | varchar |

- `product_id` is the primary key column with unique values for this table.
- This table contains data about the company's products.

#### Table: Orders

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| order_date    | date    |
| unit          | int     |

- This table may have duplicate rows.
- `product_id` is a foreign key referencing the `product_id` in the `Products` table.
- `unit` represents the number of products ordered on `order_date`.

### Task

Find the `product_name` and the total `unit` ordered for products that have at least **100 units ordered** during February 2020. Return the result in any order.

### Example

#### Input

**Products** table:

| product_id  | product_name          | product_category |
|-------------|-----------------------|------------------|
| 1           | Leetcode Solutions    | Book             |
| 2           | Jewels of Stringology | Book             |
| 3           | HP                    | Laptop           |
| 4           | Lenovo                | Laptop           |
| 5           | Leetcode Kit          | T-shirt          |

**Orders** table:

| product_id   | order_date   | unit     |
|--------------|--------------|----------|
| 1            | 2020-02-05   | 60       |
| 1            | 2020-02-10   | 70       |
| 2            | 2020-01-18   | 30       |
| 2            | 2020-02-11   | 80       |
| 3            | 2020-02-17   | 2        |
| 3            | 2020-02-24   | 3        |
| 4            | 2020-03-01   | 20       |
| 4            | 2020-03-04   | 30       |
| 4            | 2020-03-04   | 60       |
| 5            | 2020-02-25   | 50       |
| 5            | 2020-02-27   | 50       |
| 5            | 2020-03-01   | 50       |

#### Output

| product_name       | unit    |
|--------------------|---------|
| Leetcode Solutions | 130     |
| Leetcode Kit       | 100     |

---

### Thought Process

To solve this problem, we need to:

1. **Filter Orders by Date:**
   - Focus on orders placed in February 2020 (`2020-02-01` to `2020-02-29`).

2. **Join Tables:**
   - Combine `Products` and `Orders` tables using `product_id` to get product names.

3. **Calculate Total Units:**
   - Use `SUM(unit)` to calculate the total number of units ordered for each product in February 2020.

4. **Filter for Threshold:**
   - Use `HAVING` to include only products with total units ordered >= 100.

5. **Select Required Columns:**
   - Retrieve only the `product_name` and the calculated total `unit`.

---

### Solution

```sql
SELECT p.product_name, SUM(o.unit) AS unit
FROM Products p
INNER JOIN Orders o
ON p.product_id = o.product_id
WHERE o.order_date BETWEEN '2020-02-01' AND '2020-02-29'
GROUP BY p.product_name
HAVING SUM(o.unit) >= 100;
```

---

### Solution Explanation

1. **INNER JOIN:**
   - Combines rows from `Products` and `Orders` based on matching `product_id` values.

2. **WHERE Clause:**
   - Filters `Orders` to include only rows where `order_date` falls in February 2020.

3. **GROUP BY:**
   - Groups the rows by `product_name` to calculate total units per product.

4. **SUM and HAVING:**
   - Calculates the total units for each product using `SUM(unit)`.
   - Filters groups to include only those with `SUM(unit) >= 100`.

5. **SELECT Statement:**
   - Retrieves the `product_name` and the calculated total `unit` for qualifying products.

---

### Key Takeaways

- **Date Filtering:** Use `WHERE` with `BETWEEN` to specify date ranges.
- **Aggregate Functions:** Combine `GROUP BY` and `SUM` to calculate totals for grouped data.
- **Post-Aggregation Filtering:** Use `HAVING` to filter results after aggregation.
- **Joining Tables:** Use `INNER JOIN` to combine related tables based on a common column.

---

### Related Topics

- SQL Joins
- Aggregate Functions
- Date Filtering in SQL
- GROUP BY and HAVING
