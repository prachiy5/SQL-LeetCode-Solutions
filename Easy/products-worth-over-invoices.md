# Product's Worth Over Invoices

### Problem

#### Tables

**Product**:

| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| name        | varchar |

- `product_id` is the primary key.
- Contains the ID and name of each product.
- `name` consists of lowercase English letters, and no two products share the same name.

**Invoice**:

| Column Name | Type |
|-------------|------|
| invoice_id  | int  |
| product_id  | int  |
| rest        | int  |
| paid        | int  |
| canceled    | int  |
| refunded    | int  |

- `invoice_id` is the primary key.
- Contains details about invoices, including amounts for `rest`, `paid`, `canceled`, and `refunded`.

#### Task

Calculate the total amount for `rest`, `paid`, `canceled`, and `refunded` for each product across all invoices. Return the result sorted by `product_name` in ascending order.

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

### Explanation

1. **Join Tables**:
   - Use a `LEFT JOIN` to combine the `Product` table with the `Invoice` table using the `product_id` column.
   - This ensures that all products are included, even if they have no invoices.

2. **Calculate Totals**:
   - Use the `SUM` function to calculate the total for each column (`rest`, `paid`, `canceled`, `refunded`).
   - Use `IFNULL` to replace `NULL` values with `0` for products without corresponding invoice data.

3. **Group Results**:
   - Group the data by `name` to aggregate totals for each product.

4. **Order Results**:
   - Use `ORDER BY name` to sort the result table alphabetically by product name.

---

### Example Output

Given the example input:

| product_id | name  |
|------------|-------|
| 0          | ham   |
| 1          | bacon |

| invoice_id | product_id | rest | paid | canceled | refunded |
|------------|------------|------|------|----------|----------|
| 23         | 0          | 2    | 0    | 5        | 0        |
| 12         | 0          | 0    | 4    | 0        | 3        |
| 1          | 1          | 1    | 1    | 0        | 1        |
| 2          | 1          | 1    | 0    | 1        | 1        |
| 3          | 1          | 0    | 1    | 1        | 1        |
| 4          | 1          | 1    | 1    | 1        | 0        |

Output:

| name  | rest | paid | canceled | refunded |
|-------|------|------|----------|----------|
| bacon | 3    | 3    | 3        | 3        |
| ham   | 2    | 4    | 5        | 3        |

---

### Key Takeaways

- Use `LEFT JOIN` to ensure all rows from one table are included, even if there are no matches in the other table.
- Use `SUM` to calculate totals and `IFNULL` to handle missing data gracefully.
- Use `GROUP BY` for aggregating totals by a specific column.
- Always ensure proper sorting (`ORDER BY`) as per the task requirements.
