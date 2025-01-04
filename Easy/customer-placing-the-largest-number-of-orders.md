# Find Customer with Largest Number of Orders

### Problem

#### Table: Orders

| Column Name     | Type |
|-----------------|------|
| order_number    | int  |
| customer_number | int  |

- `order_number` is the primary key column with unique values.
- This table contains information about the order ID and the customer ID.

#### Task
Find the `customer_number` for the customer who has placed the largest number of orders.

If multiple customers have the same largest number of orders, include all such `customer_number` values.

#### Example

**Input:**

`Orders` table:

| order_number | customer_number |
|--------------|-----------------|
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |

**Output:**

| customer_number |
|-----------------|
| 3               |

**Explanation:**
- Customer `3` has placed two orders, which is more than customers `1` and `2` who have only one order each.
- Therefore, the result is `customer_number = 3`.

---

### Thought Process

To solve this problem, the goal is to identify the customer(s) with the highest number of orders. Here’s how I approached it step by step:

1. **Count Orders per Customer:**
   - Use `COUNT(order_number)` grouped by `customer_number` to calculate the total orders for each customer.

2. **Find the Maximum Orders:**
   - Use a Common Table Expression (CTE) to calculate the total orders per customer.
   - Use a subquery to find the maximum number of orders from the CTE.

3. **Filter Customers with Maximum Orders:**
   - Retrieve the `customer_number` of customers whose order count matches the maximum.

4. **Handle Ties (Optional):**
   - If multiple customers have the same maximum orders, include all such customers in the output.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT COUNT(order_number) AS orders, customer_number
  FROM Orders
  GROUP BY customer_number
)

SELECT customer_number
FROM cte
WHERE orders = (SELECT MAX(orders) FROM cte);
```

---

### Solution Explanation

- **WITH Clause:**
  - The CTE calculates the total orders (`orders`) for each `customer_number` using `COUNT(order_number)` grouped by `customer_number`.

- **Subquery:**
  - The subquery `(SELECT MAX(orders) FROM cte)` finds the maximum number of orders in the CTE.

- **Filter Condition:**
  - The `WHERE` clause filters the CTE to only include rows where `orders` equals the maximum number of orders.

---

### Key Takeaways

- **CTEs (Common Table Expressions):** Useful for breaking down complex queries into manageable steps.
- **Aggregate Functions:** Use `COUNT()` to count rows and `MAX()` to find the largest value.
- **Handling Ties:** Using a `WHERE` clause allows us to include all customers with the highest order count in case of a tie.

---

### Related Topics
- Aggregate Functions in SQL
- GROUP BY and Filtering
- Using Common Table Expressions (CTEs)
