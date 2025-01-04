# Rearrange Products Table

### Problem

The goal is to transform the `Products` table so that each row represents a combination of `product_id`, `store`, and `price`. If a product is not available in a store (indicated by a `NULL` value), it should not be included in the result.

### Input
**Products Table:**

| product_id | store1 | store2 | store3 |
|------------|--------|--------|--------|
| 0          | 95     | 100    | 105    |
| 1          | 70     | NULL   | 80     |

### Output
**Expected Output Table:**

| product_id | store  | price |
|------------|--------|-------|
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |

---

### Thought Process

1. **Identify Valid Data:** Exclude rows where the `price` is `NULL` for each store.
2. **Normalize the Data:** Use `UNION ALL` to merge results from three separate queries (one for each store).
3. **Add Store Name:** Use hardcoded strings (`'store1'`, `'store2'`, `'store3'`) to represent the store name in the output.
4. **Return Combined Data:** Merge all the rows from the `UNION ALL` queries into a single result.

---

### Solution

```sql
SELECT product_id, 'store1' AS store, store1 AS price
FROM products
WHERE store1 IS NOT NULL

UNION ALL

SELECT product_id, 'store2' AS store, store2 AS price
FROM products
WHERE store2 IS NOT NULL

UNION ALL

SELECT product_id, 'store3' AS store, store3 AS price
FROM products
WHERE store3 IS NOT NULL;
```

---

### Explanation

1. **Query for `store1`:**
   - Select `product_id`, `'store1'` as store name, and `store1` price.
   - Exclude rows where `store1` is `NULL`.

2. **Query for `store2`:**
   - Repeat the same logic for `store2`.

3. **Query for `store3`:**
   - Repeat the same logic for `store3`.

4. **Combine Results:**
   - Use `UNION ALL` to merge results from the three queries.

5. **Output:**
   - The result table includes all valid `product_id`, `store`, and `price` combinations.

---

### Key Takeaways
- `UNION ALL` is used to combine rows from multiple queries without removing duplicates.
- Hardcoded store names (`'store1'`, `'store2'`, `'store3'`) help identify the source store in the output.
- Filtering out `NULL` values ensures that only available products are included in the result.

---
