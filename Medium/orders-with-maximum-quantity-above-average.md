### Orders With Maximum Quantity Above Average

**Table: OrdersDetails**
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| order_id    | int  |
| product_id  | int  |
| quantity    | int  |
+-------------+------+
```
(order_id, product_id) is the primary key (combination of columns with unique values) for this table.
A single order is represented as multiple rows, one row for each product in the order.
Each row of this table contains the quantity ordered of the product `product_id` in the order `order_id`.

You are running an e-commerce site that is looking for imbalanced orders. An imbalanced order is one whose maximum quantity is strictly greater than the average quantity of every order (including itself).

The average quantity of an order is calculated as:

\[ \text{average quantity} = \frac{\text{total quantity of all products in the order}}{\text{number of different products in the order}} \]

The maximum quantity of an order is the highest quantity of any single product in the order.

Write a solution to find the `order_id` of all imbalanced orders.

Return the result table in any order.

### Example 1:

**Input:**

OrdersDetails table:

| order_id | product_id | quantity |
|----------|------------|----------|
| 1        | 1          | 12       |
| 1        | 2          | 10       |
| 1        | 3          | 15       |
| 2        | 1          | 8        |
| 2        | 4          | 4        |
| 2        | 5          | 6        |
| 3        | 3          | 5        |
| 3        | 4          | 18       |
| 4        | 5          | 2        |
| 4        | 6          | 8        |
| 5        | 7          | 9        |
| 5        | 8          | 9        |
| 3        | 9          | 20       |
| 2        | 9          | 4        |

**Output:**

| order_id |
|----------|
| 1        |
| 3        |

**Explanation:**

The average quantity of each order is:
- order_id=1: \((12+10+15)/3 = 12.333\ldots\)
- order_id=2: \((8+4+6+4)/4 = 5.5\)
- order_id=3: \((5+18+20)/3 = 14.333\ldots\)
- order_id=4: \((2+8)/2 = 5\)
- order_id=5: \((9+9)/2 = 9\)

The maximum quantity of each order is:
- order_id=1: \(\text{max}(12, 10, 15) = 15\)
- order_id=2: \(\text{max}(8, 4, 6, 4) = 8\)
- order_id=3: \(\text{max}(5, 18, 20) = 20\)
- order_id=4: \(\text{max}(2, 8) = 8\)
- order_id=5: \(\text{max}(9, 9) = 9\)

Orders 1 and 3 are imbalanced because they have a maximum quantity that exceeds the average quantity of every order.

---

### Thought Process

1. **Calculate the Average Quantity and Maximum Quantity Per Order**:
   - Use `SUM(quantity)` to calculate the total quantity for each `order_id`.
   - Use `COUNT(DISTINCT product_id)` to find the number of products in each order.
   - Use `MAX(quantity)` to determine the maximum quantity for each `order_id`.

2. **Compare the Maximum Quantity with the Global Maximum Average**:
   - Compute the maximum average quantity across all orders.
   - Identify the orders where the `max_quantity` is greater than the global maximum average.

3. **Return the `order_id` of Imbalanced Orders**:
   - Filter and return only those orders that meet the imbalance criteria.

---

### Query

```sql
WITH cte AS (
    SELECT order_id, 
           SUM(quantity) / COUNT(DISTINCT product_id) AS avg_qty,
           MAX(quantity) AS max_quantity 
    FROM OrdersDetails
    GROUP BY order_id
),
cte2 AS (
    SELECT MAX(avg_qty) AS m FROM cte
)
SELECT order_id 
FROM cte, cte2
WHERE max_quantity > m;
