### Product Price at a Given Date
We are given a table:

#### Table: `Products`

| Column Name   | Type |
|---------------|------|
| product_id    | int  |
| new_price     | int  |
| change_date   | date |

- `(product_id, change_date)` is the primary key, meaning each combination of `product_id` and `change_date` is unique.
- Each row indicates that the price of a product was updated to `new_price` on the `change_date`.

### Task
Write a SQL query to find the prices of all products on `2019-08-16`. Assume the price of all products before any change is `10`.

The result table should include the `product_id` and the corresponding `price`. The result can be returned in any order.

### Example
#### Input:
**Products table:**

| product_id | new_price | change_date |
|------------|-----------|-------------|
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |

#### Output:

| product_id | price |
|------------|-------|
| 1          | 35    |
| 2          | 50    |
| 3          | 10    |

#### Explanation:
- On `2019-08-16`:
  - For `product_id = 1`, the price was updated to `35` on that day.
  - For `product_id = 2`, the most recent price before `2019-08-16` was `50` (on `2019-08-14`).
  - For `product_id = 3`, there was no price change before or on `2019-08-16`, so the default price is `10`.

---

### Thought Process
1. **Identify Latest Price Change Before the Target Date**: Use a Common Table Expression (CTE) with the `DENSE_RANK()` window function to rank price changes for each product in descending order of `change_date`.
   - Filter rows to include only price changes that occurred on or before `2019-08-16`.
   - Assign a rank to each change per `product_id` based on its recency.
2. **Get Latest Price for Each Product**: Select rows where the rank is `1` (i.e., the most recent price change before or on `2019-08-16`).
3. **Handle Products Without Price Changes**: Include products that have no price changes before `2019-08-16` and assign them the default price of `10`.
4. **Combine Results**: Use `UNION` to merge the results from the previous steps and return the final list of product prices.

---

### SQL Query
```sql
WITH new AS (
    SELECT 
        *, 
        DENSE_RANK() OVER (PARTITION BY product_id ORDER BY change_date DESC) AS rn
    FROM Products
    WHERE change_date <= '2019-08-16'
)

SELECT product_id, new_price AS price
FROM new
WHERE rn = 1

UNION

SELECT product_id, 10 AS price
FROM Products
WHERE product_id NOT IN (
    SELECT product_id FROM new
);
```

---

### Key Takeaways
1. **Window Functions**: Use `DENSE_RANK()` to rank rows within each `product_id` by `change_date` in descending order, making it easy to select the most recent price.
2. **Default Values**: Handle cases where no price change occurred before the target date by using a default value (`10`).
3. **Combining Results**: Use `UNION` to combine results from two different scenarios (with and without price changes).
4. **Subqueries**: Identify products without any price changes using `NOT IN`.

### Related Topics
- Window Functions (`DENSE_RANK`, `ROW_NUMBER`)
- Conditional Filtering
- Handling Missing Data
- Combining Queries with `UNION`
