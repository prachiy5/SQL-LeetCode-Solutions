### Calculate Compressed Mean

Table: Orders

```
+-------------------+------+
| Column Name       | Type |
+-------------------+------+
| order_id          | int  |
| item_count        | int  |
| order_occurrences | int  |
+-------------------+------+
```

`order_id` is a column of unique values for this table.
This table contains `order_id`, `item_count`, and `order_occurrences`.

Write a solution to calculate the average number of items per order, rounded to 2 decimal places.

Return the result table in any order.

The result format is in the following example:

#### Example 1:

**Input:**

Orders table:

```
+----------+------------+-------------------+
| order_id | item_count | order_occurrences | 
+----------+------------+-------------------+
| 10       | 1          | 500               | 
| 11       | 2          | 1000              |     
| 12       | 3          | 800               |  
| 13       | 4          | 1000              | 
+----------+------------+-------------------+
```

**Output:**

```
+-------------------------+
| average_items_per_order | 
+-------------------------+
| 2.70                    |
+-------------------------+
```

**Explanation:**

The calculation is as follows:
- Total items: `(1 * 500) + (2 * 1000) + (3 * 800) + (4 * 1000) = 8900`
- Total orders: `500 + 1000 + 800 + 1000 = 3300`
- Therefore, the average items per order is `8900 / 3300 = 2.70`.

---

### Thought Process:

1. **Understand the Requirement:** We need to calculate the average number of items per order. The result should be rounded to 2 decimal places.
2. **Key Points:**
   - The total number of items for all orders is calculated as the sum of `item_count * order_occurrences`.
   - The total number of orders is the sum of `order_occurrences`.
   - The average is calculated by dividing the total number of items by the total number of orders.
3. **Steps:**
   - Use the `SUM` function to calculate the total items and total orders.
   - Use the `ROUND` function to round the result to 2 decimal places.
   - Return the result as `average_items_per_order`.

---

### Query:

```sql
SELECT ROUND(SUM(item_count * order_occurrences) / SUM(order_occurrences), 2) AS average_items_per_order
FROM orders;
```

---

### Explanation of the Query:

1. **Calculate Total Items:**
   - `SUM(item_count * order_occurrences)` computes the total number of items for all orders.
2. **Calculate Total Orders:**
   - `SUM(order_occurrences)` computes the total number of orders.
3. **Calculate Average Items per Order:**
   - Divide the total items by the total orders and round the result to 2 decimal places using `ROUND`.
4. **Alias:**
   - Use `AS average_items_per_order` to name the output column.

---

### Why This Query Works:
- It correctly calculates the weighted average of items per order based on the number of occurrences.
- The use of `ROUND` ensures the result is formatted to two decimal places, as required.
