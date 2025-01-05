### Customers Who Bought All Products

Table: Customer

```sql
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+
```

This table may contain duplicate rows. 
- `customer_id` is not NULL.
- `product_key` is a foreign key (reference column) to the `Product` table.

Table: Product

```sql
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
```

- `product_key` is the primary key (column with unique values) for this table.

Write a solution to report the `customer_id` from the `Customer` table that bought all the products in the `Product` table.

Return the result table in any order.

### Example

#### Input: 
Customer table:
```sql
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+
```

Product table:
```sql
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+
```

#### Output:
```sql
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+
```

### Explanation: 
The customers who bought all the products (5 and 6) are customers with IDs 1 and 3.

---

### Thought Process
1. **Understand the Requirements**:
   - Identify which customers purchased all the products listed in the `Product` table.
   - Use the `Product` table to determine the total count of unique products.
   - Compare each customer's purchased product count to the total product count.
2. **Group and Aggregate Data**:
   - Group by `customer_id` in the `Customer` table.
   - Count the distinct `product_key` values for each customer.
3. **Filter Results**:
   - Ensure the customer's distinct product count matches the total product count in the `Product` table.
4. **Return the Results**:
   - Select and return the matching `customer_id` values.

---

### Query

```sql
SELECT customer_id
FROM customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (
    SELECT COUNT(product_key) 
    FROM product
);
```

### Explanation
1. **Subquery**:
   - `SELECT COUNT(product_key) FROM product`: Calculates the total number of unique products in the `Product` table.
2. **Group by Customer**:
   - `GROUP BY customer_id`: Groups the data by each `customer_id`.
3. **Filter by Product Count**:
   - `HAVING COUNT(DISTINCT product_key) = ...`: Ensures only customers who purchased all the products are included in the result.
4. **Output**:
   - Returns the `customer_id` values that meet the criteria.

---

### Output Example

For the input tables provided:

#### Customer Table:
```sql
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+
```

#### Product Table:
```sql
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+
```

#### Query Output:
```sql
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+
