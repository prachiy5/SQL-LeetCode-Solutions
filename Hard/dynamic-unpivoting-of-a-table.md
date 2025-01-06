# Dynamic Unpivoting of a Table

## Table: Products
| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| store_name1 | int     |
| store_name2 | int     |
|      :      | int     |
|      :      | int     |
|      :      | int     |
| store_namen | int     |

- `product_id` is the primary key for this table.
- Each row in this table indicates the product's price in `n` different stores.
- If the product is not available in a store, the price will be `null` in that store's column.
- The names of the stores may change from one testcase to another.
- There will be at least 1 store and at most 30 stores.

## Task
Reorganize the `Products` table so that:
1. Each row has the ID of one product, the name of a store where it is sold, and its price in that store.
2. If a product is not available in a store, **do not include** a row with that `product_id` and `store` combination in the result table.
3. The result table should have three columns: `product_id`, `store`, and `price`.

The procedure `UnpivotProducts` should return the reorganized table.

Return the result table in any order.

---

# Example

### Input

#### Products Table:
```
| product_id | LC_Store | Nozama | Shop | Souq |
|------------|----------|--------|------+------|
| 1          | 100      | null   | 110  | null |
| 2          | null     | 200    | null | 190  |
| 3          | null     | null   | 1000 | 1900 |
```
### Output
| product_id | store    | price |
|------------|----------|-------|
| 1          | LC_Store | 100   |
| 1          | Shop     | 110   |
| 2          | Nozama   | 200   |
| 2          | Souq     | 190   |
| 3          | Shop     | 1000  |
| 3          | Souq     | 1900  |

### Explanation
1. For `product_id = 1`, it is sold in `LC_Store` and `Shop` with prices 100 and 110 respectively.
2. For `product_id = 2`, it is sold in `Nozama` and `Souq` with prices 200 and 190 respectively.
3. For `product_id = 3`, it is sold in `Shop` and `Souq` with prices 1000 and 1900 respectively.
4. Columns with `null` values (indicating the product is not sold in that store) are excluded from the output.

---

# Thought Process

1. **Dynamic Columns**:
   - The column names for stores are dynamic and can change for each test case. We need to dynamically fetch these column names.

2. **Filter Non-Null Prices**:
   - For each store column, include only those rows where the price is not `null`.

3. **Union All Store Data**:
   - Combine the data for all stores into a single result set using `UNION`.

4. **Dynamic SQL Query**:
   - Use `information_schema.columns` to get the column names dynamically.
   - Generate a query dynamically to unpivot the data.
   - Use `PREPARE` and `EXECUTE` for dynamic query execution in MySQL.

---

# Solution

```sql
CREATE PROCEDURE UnpivotProducts()
BEGIN
	# Write your MySQL query statement below.
    SET session group_concat_max_len = 1000000;
    
    SET @macro = NULL;
    
	SELECT GROUP_CONCAT(
        CONCAT(
            'SELECT product_id, "', `column_name`, '" AS store, ', `column_name`,
            ' AS price FROM products WHERE ', `column_name`, ' IS NOT NULL'
        ) SEPARATOR ' UNION '
    )
        INTO @macro
        FROM `information_schema`.`columns`
        WHERE `table_schema` = 'test' AND `table_name` = 'products' AND `column_name` != 'product_id';
    
    PREPARE sql_query FROM @macro;
    EXECUTE sql_query;
    DEALLOCATE PREPARE sql_query;
END
```

---

# Solution Explanation

1. **Set Group Concat Length**:
   - The `GROUP_CONCAT` function has a default length limit. Increase it to handle longer queries.

2. **Generate Dynamic Query**:
   - Use `information_schema.columns` to fetch all column names in the `Products` table except `product_id`.
   - For each column (store), generate a `SELECT` statement that retrieves the `product_id`, store name, and price where the price is not `null`.
   - Combine these `SELECT` statements using `UNION`.

3. **Prepare and Execute**:
   - Combine the dynamic query into a single string and execute it using `PREPARE` and `EXECUTE`.

4. **Clean Up**:
   - Use `DEALLOCATE PREPARE` to clean up the prepared statement.

---

# Key Takeaways
- Use `information_schema.columns` to handle dynamic columns in SQL.
- `GROUP_CONCAT` is helpful for generating dynamic SQL queries.
- Always clean up dynamic SQL statements with `DEALLOCATE PREPARE`.
- Dynamic queries are essential for handling changing table schemas.

---

# Related Topics
- Dynamic SQL
- String Functions (`GROUP_CONCAT`, `CONCAT`)
- Conditional Filters (`IS NOT NULL`)
- Stored Procedures
