### The Most Recent Three Orders
#### Table: Customers

```sql
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
+---------------+---------+
```
- `customer_id` is the column with unique values for this table.
- This table contains information about customers.

#### Table: Orders

```sql
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| customer_id   | int     |
| cost          | int     |
+---------------+---------+
```
- `order_id` is the column with unique values for this table.
- This table contains information about the orders made by `customer_id`.
- Each customer has one order per day.

#### Problem Statement

Write a solution to find the most recent three orders of each user. If a user ordered less than three orders, return all of their orders.

Return the result table ordered by `customer_name` in ascending order, and in case of a tie, by the `customer_id` in ascending order. If there is still a tie, order them by `order_date` in descending order.

#### Result Format
The result should have the following columns:
- `customer_name`
- `customer_id`
- `order_id`
- `order_date`

### Example

#### Input

**Customers**

```sql
+-------------+-----------+
| customer_id | name      |
+-------------+-----------+
| 1           | Winston   |
| 2           | Jonathan  |
| 3           | Annabelle |
| 4           | Marwan    |
| 5           | Khaled    |
+-------------+-----------+
```

**Orders**

```sql
+----------+------------+-------------+------+
| order_id | order_date | customer_id | cost |
+----------+------------+-------------+------+
| 1        | 2020-07-31 | 1           | 30   |
| 2        | 2020-07-30 | 2           | 40   |
| 3        | 2020-07-31 | 3           | 70   |
| 4        | 2020-07-29 | 4           | 100  |
| 5        | 2020-06-10 | 1           | 1010 |
| 6        | 2020-08-01 | 2           | 102  |
| 7        | 2020-08-01 | 3           | 111  |
| 8        | 2020-08-03 | 1           | 99   |
| 9        | 2020-08-07 | 2           | 32   |
| 10       | 2020-07-15 | 1           | 2    |
+----------+------------+-------------+------+
```

#### Output

```sql
+---------------+-------------+----------+------------+
| customer_name | customer_id | order_id | order_date |
+---------------+-------------+----------+------------+
| Annabelle     | 3           | 7        | 2020-08-01 |
| Annabelle     | 3           | 3        | 2020-07-31 |
| Jonathan      | 2           | 9        | 2020-08-07 |
| Jonathan      | 2           | 6        | 2020-08-01 |
| Jonathan      | 2           | 2        | 2020-07-30 |
| Marwan        | 4           | 4        | 2020-07-29 |
| Winston       | 1           | 8        | 2020-08-03 |
| Winston       | 1           | 1        | 2020-07-31 |
| Winston       | 1           | 10       | 2020-07-15 |
+---------------+-------------+----------+------------+
```

---

### Thought Process
1. **Understand the Relationship**: Each `customer_id` in the `Customers` table can have multiple entries in the `Orders` table.
2. **Identify the Most Recent Orders**: For each customer, identify their most recent three orders based on the `order_date`.
3. **Order and Group the Data**:
   - Use a window function to rank the orders by `order_date` for each `customer_id`.
   - Filter for the top 3 most recent orders.
4. **Output Requirements**:
   - Include `customer_name`, `customer_id`, `order_id`, and `order_date`.
   - Sort the result by `customer_name` (ascending), then by `customer_id` (ascending), and finally by `order_date` (descending).

---

### Query

```sql
WITH cte AS (
    SELECT 
        c.name AS customer_name,
        c.customer_id,
        o.order_id,
        o.order_date,
        DENSE_RANK() OVER (PARTITION BY c.customer_id ORDER BY o.order_date DESC) AS r
    FROM customers c
    INNER JOIN orders o
        ON c.customer_id = o.customer_id
)
SELECT 
    customer_name,
    customer_id,
    order_id,
    order_date
FROM cte 
WHERE r <= 3
ORDER BY customer_name, customer_id, order_date DESC;
