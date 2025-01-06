# Dynamic Pivoting of a Table

## Table: Products
| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| store       | varchar |
| price       | int     |

- `(product_id, store)` is the primary key (combination of columns with unique values) for this table.
- Each row of this table indicates the price of `product_id` in a `store`.
- There will be at most 30 different stores in the table.
- `price` is the price of the product at this store.

## Task
Reorganize the `Products` table so that:
1. Each row has the ID of one product and its price in each store.
2. The price should be `null` if the product is not sold in a store.
3. The columns of the table should contain each store, sorted in **lexicographical order**.

Implement the procedure `PivotProducts` to achieve this. The procedure should return the table after reorganizing it.

Return the result table in any order.

---

# Example

### Input

#### Products Table:
| product_id | store    | price |
|------------|----------|-------|
| 1          | Shop     | 110   |
| 1          | LC_Store | 100   |
| 2          | Nozama   | 200   |
| 2          | Souq     | 190   |
| 3          | Shop     | 1000  |
| 3          | Souq     | 1900  |

### Output
```
| product_id | LC_Store | Nozama | Shop | Souq |
|------------|----------|--------|------+------|
| 1          | 100      | null   | 110  | null |
| 2          | null     | 200    | null | 190  |
| 3          | null     | null   | 1000 | 1900 |
```
### Explanation
1. There are 4 stores: `Shop`, `LC_Store`, `Nozama`, and `Souq`. They are ordered lexicographically as: `LC_Store`, `Nozama`, `Shop`, and `Souq`.
2. For `product_id = 1`, the price is 100 in `LC_Store` and 110 in `Shop`. It is not sold in `Nozama` and `Souq`, so their prices are `null`.
3. For `product_id = 2`, the price is 200 in `Nozama` and 190 in `Souq`. It is not sold in `LC_Store` and `Shop`, so their prices are `null`.
4. For `product_id = 3`, the price is 1000 in `Shop` and 1900 in `Souq`. It is not sold in `LC_Store` and `Nozama`, so their prices are `null`.

---

# Thought Process

1. **Dynamic Column Creation**:
   - Since the number of stores can vary and their names are dynamic, the columns for each store need to be created dynamically.

2. **Aggregate Prices for Each Store**:
   - For each store, check if the product is sold there.
   - Use `IF` to assign the price if the store matches, otherwise assign `null`.

3. **Dynamic SQL**:
   - Use `GROUP_CONCAT` to dynamically create a list of `SUM(IF(...))` statements for each store.
   - Order the store names lexicographically to ensure the columns are sorted.

4. **Execute the Query**:
   - Build the complete SQL query as a string.
   - Use `PREPARE`, `EXECUTE`, and `DEALLOCATE` to run the dynamic SQL query inside the procedure.

---

# Solution

```sql
CREATE PROCEDURE PivotProducts()
BEGIN
    # Write your MySQL query statement below.
    SET group_concat_max_len = 1000000; # This is tricky. There's a length limit on GROUP_CONCAT.
    SET @sql = NULL;

    SELECT
        GROUP_CONCAT(DISTINCT CONCAT(
          'SUM(IF(store = "', store, '", price, null)) AS ', store) ORDER BY store ASC)
    INTO @sql
    FROM Products;

    SET @sql = CONCAT('SELECT product_id, ', @sql, ' FROM Products GROUP BY product_id');

    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
END
```

---

# Solution Explanation

1. **Set Group Concat Length**:
   - `GROUP_CONCAT` has a default length limit. Set it to a high value to handle longer queries.

2. **Generate Dynamic Columns**:
   - Use `GROUP_CONCAT` to dynamically create a `SUM(IF(...))` statement for each store.
   - Each store is handled as `SUM(IF(store = 'store_name', price, null))`.
   - Order the store names lexicographically using `ORDER BY`.

3. **Build the Query**:
   - Concatenate the generated column statements with a `SELECT` query.
   - Group the results by `product_id` to ensure each row represents one product.

4. **Execute the Query**:
   - Use `PREPARE` to prepare the dynamic SQL query.
   - Use `EXECUTE` to run the query.
   - Use `DEALLOCATE` to clean up after execution.

---

# Key Takeaways
- Use `GROUP_CONCAT` to dynamically generate SQL queries.
- Handle dynamic columns by creating them on the fly using string concatenation.
- Use `PREPARE` and `EXECUTE` for dynamic SQL execution in MySQL.
- Always sort columns lexicographically if required by the problem.

---

# Related Topics
- Dynamic SQL
- String Functions (`GROUP_CONCAT`, `CONCAT`)
- Conditional Aggregates (`SUM`, `IF`)
- Stored Procedures
