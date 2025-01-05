### Products With Three or More Orders in Two Consecutive Years

**Table:** Orders
```
+---------------+------+
| Column Name   | Type |
+---------------+------+
| order_id      | int  |
| product_id    | int  |
| quantity      | int  |
| purchase_date | date |
+---------------+------+
```
`order_id` contains unique values.

Each row in this table contains the ID of an order, the ID of the product purchased, the quantity, and the purchase date.

Write a solution to report the IDs of all the products that were ordered three or more times in two consecutive years.

Return the result table in any order.

**Example 1:**

Input:

Orders table:

```
+----------+------------+----------+---------------+
| order_id | product_id | quantity | purchase_date |
+----------+------------+----------+---------------+
| 1        | 1          | 7        | 2020-03-16    |
| 2        | 1          | 4        | 2020-12-02    |
| 3        | 1          | 7        | 2020-05-10    |
| 4        | 1          | 6        | 2021-12-23    |
| 5        | 1          | 5        | 2021-05-21    |
| 6        | 1          | 6        | 2021-10-11    |
| 7        | 2          | 6        | 2022-10-11    |
+----------+------------+----------+---------------+
```

Output:

```
+------------+
| product_id |
+------------+
| 1          |
+------------+
```

**Explanation:**

- Product `1` was ordered in 2020 three times and in 2021 three times. Since it was ordered three times in two consecutive years, we include it in the answer.
- Product `2` was ordered one time in 2022. We do not include it in the answer.

---

### Thought Process

To solve this problem:
1. Identify products that have at least three orders in a single year by grouping by `product_id` and the year extracted from `purchase_date`.
2. Filter the results to include only products with three or more orders per year.
3. Check for consecutive years where the same product meets the condition by joining the table to itself and ensuring the years are consecutive.
4. Select the distinct product IDs from the result.

---

### Query

```sql
WITH cte AS (
    SELECT 
        product_id,
        YEAR(purchase_date) AS purchase_year
    FROM orders
    GROUP BY product_id, purchase_year
    HAVING COUNT(order_id) >= 3
)

SELECT DISTINCT c1.product_id
FROM cte c1
JOIN cte c2
  ON c1.product_id = c2.product_id
  AND c1.purchase_year = c2.purchase_year + 1;
