# Product's Price for Each Store

### Problem

#### Table: Products

| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| store       | enum    |
| price       | int     |

- `(product_id, store)` is the primary key.
- `store` is an ENUM with values ('store1', 'store2', 'store3').
- `price` represents the product's price at the respective store.

#### Task
Find the price of each product in each store. If a product is not sold at a particular store, return `NULL` for that store.

### Example

#### Input

**Products Table**:

| product_id | store  | price |
|------------|--------|-------|
| 0          | store1 | 95    |
| 0          | store3 | 105   |
| 0          | store2 | 100   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |

#### Output

| product_id | store1 | store2 | store3 |
|------------|--------|--------|--------|
| 0          | 95     | 100    | 105    |
| 1          | 70     | NULL   | 80     |

#### Explanation
- Product `0` is available in all three stores with prices 95, 100, and 105 respectively.
- Product `1` is available in `store1` and `store3` but not in `store2`, so the price for `store2` is `NULL`.

### Thought Process

1. **Understand the Problem**:
   - Group data by `product_id`.
   - Extract the price for each store (`store1`, `store2`, and `store3`).
   - If a product is not available in a store, return `NULL` for that store.

2. **Apply Conditional Logic**:
   - Use the `CASE` statement to conditionally fetch prices for each store.

3. **Aggregate Results**:
   - Use `SUM` or `MAX` (both work here as there's only one row per product-store pair) to consolidate results for each store.

4. **Group Data**:
   - Group by `product_id` to ensure one row per product.

### Solution

```sql
SELECT 
    product_id,
    SUM(CASE WHEN store = 'store1' THEN price END) AS store1,
    SUM(CASE WHEN store = 'store2' THEN price END) AS store2,
    SUM(CASE WHEN store = 'store3' THEN price END) AS store3
FROM products
GROUP BY product_id;
```

### Solution Explanation

1. **SELECT Clause**:
   - Extract `product_id` to identify each product.
   - Use `CASE` to conditionally select prices for each store.
   - `SUM` consolidates the prices for each product-store pair, returning `NULL` if the condition is not met.

2. **GROUP BY Clause**:
   - Groups rows by `product_id`, ensuring one result row per product.

3. **Result**:
   - Produces a table showing each product's prices in all stores, with `NULL` for stores where the product is not available.

### Key Takeaways

- Use `CASE` for conditional logic in SQL.
- Aggregate functions like `SUM` or `MAX` can be used to consolidate data.
- `GROUP BY` ensures data is grouped appropriately for calculations.

### Related Topics

- Conditional Logic with `CASE`
- SQL Aggregations
- Data Grouping with `GROUP BY`
