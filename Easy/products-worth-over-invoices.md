# Product's Worth Over Invoices
### Problem

#### Table: Product

| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| name        | varchar |

- `product_id` is the primary key column.
- This table contains the ID and name of the product.
- The `name` column consists of lowercase English letters, and no two products share the same name.

#### Table: Invoice

| Column Name | Type |
|-------------|------|
| invoice_id  | int  |
| product_id  | int  |
| rest        | int  |
| paid        | int  |
| canceled    | int  |
| refunded    | int  |

- `invoice_id` is the primary key column.
- Contains details about invoices, including amounts for `rest`, `paid`, `canceled`, and `refunded`.

#### Task

For each product, calculate the total amount for the columns `rest`, `paid`, `canceled`, and `refunded` across all invoices.
Return the result table ordered by the product name.

#### Example

**Input**:

**Product Table**:

| product_id | name  |
|------------|-------|
| 0          | ham   |
| 1          | bacon |

**Invoice Table**:

| invoice_id | product_id | rest | paid | canceled | refunded |
|------------|------------|------|------|----------|----------|
| 23         | 0          | 2    | 0    | 5        | 0        |
| 12         | 0          | 0    | 4    | 0        | 3        |
| 1          | 1          | 1    | 1    | 0        | 1        |
| 2          | 1          | 1    | 0    | 1        | 1        |
| 3          | 1          | 0    | 1    | 1        | 1        |
| 4          | 1          | 1    | 1    | 1        | 0        |

**Output**:

| name  | rest | paid | canceled | refunded |
|-------|------|------|----------|----------|
| bacon | 3    | 3    | 3        | 3        |
| ham   | 2    | 4    | 5        | 3        |

**Explanation**:

- The total `rest` for bacon: 1 + 1 + 0 + 1 = 3
- The total `paid` for bacon: 1 + 0 + 1 + 1 = 3
- The total `canceled` for bacon: 0 + 1 + 1 + 1 = 3
- The total `refunded` for bacon: 1 + 1 + 1 + 0 = 3
- Similarly, the totals for ham are calculated.

---

### Thought Process

1. **Understand the Tables**:
   - `Product` table has unique product names and IDs.
   - `Invoice` table contains financial details for each product.

2. **Join the Tables**:
   - Use `LEFT JOIN` to combine `Product` and `Invoice` tables on `product_id`.
   - This ensures all products are included, even those without invoices.

3. **Aggregate Data**:
   - Use `SUM` to calculate totals for `rest`, `paid`, `canceled`, and `refunded` for each product.
   - Use `IFNULL` to handle cases where a product has no associated invoices (i.e., null values).

4. **Group and Sort Results**:
   - Use `GROUP BY name` to aggregate totals for each product.
   - Sort the results by `name` in ascending order.

---

### Solution

```sql
SELECT 
    name, 
    IFNULL(SUM(rest), 0) AS rest,
    IFNULL(SUM(paid), 0) AS paid,
    IFNULL(SUM(canceled), 0) AS canceled,
    IFNULL(SUM(refunded), 0) AS refunded
FROM 
    Product p 
LEFT JOIN 
    Invoice i
ON 
    p.product_id = i.product_id
GROUP BY 
    name
ORDER BY 
    name;
```

---

### Solution Explanation

1. **Join Tables**:
   - Combine `Product` and `Invoice` using `LEFT JOIN` on the `product_id` column.
   - This ensures all products are included, even if they don’t have invoices.

2. **Aggregate Totals**:
   - Use `SUM` to compute the total values for `rest`, `paid`, `canceled`, and `refunded`.
   - Use `IFNULL` to replace null values with 0 for products without invoices.

3. **Group and Order**:
   - Use `GROUP BY name` to group totals by product name.
   - Use `ORDER BY name` to sort the results alphabetically by product name.

---

### Key Takeaways

- Use `LEFT JOIN` to include all rows from one table, even if there are no matching rows in the other table.
- Use `SUM` to calculate totals and `IFNULL` to handle null values.
- Use `GROUP BY` for aggregation and `ORDER BY` for sorting results.

---

### Related Topics

- SQL Joins
- Aggregation Functions
- Handling Null Values in SQL
