# Drop Type 1 Orders for Customers With Type 0 Orders

### Problem Description
We are tasked to filter and report orders from a given table `Orders` based on specific conditions:
1. If a customer has at least one order of type `0`, we do not report any order of type `1` from that customer.
2. If a customer does not have any order of type `0`, we report all their orders (both type `0` and type `1`).

The result should be returned in any order.

### Input Example

**Orders Table:**

| order_id | customer_id | order_type |
|----------|-------------|------------|
| 1        | 1           | 0          |
| 2        | 1           | 0          |
| 11       | 2           | 0          |
| 12       | 2           | 1          |
| 21       | 3           | 1          |
| 22       | 3           | 0          |
| 31       | 4           | 1          |
| 32       | 4           | 1          |

### Output Example

| order_id | customer_id | order_type |
|----------|-------------|------------|
| 31       | 4           | 1          |
| 32       | 4           | 1          |
| 1        | 1           | 0          |
| 2        | 1           | 0          |
| 11       | 2           | 0          |
| 22       | 3           | 0          |

### Explanation

1. Customer 1 has two orders of type `0`. We return both orders.
2. Customer 2 has one order of type `0` and one order of type `1`. We only return the order of type `0`.
3. Customer 3 has one order of type `0` and one order of type `1`. We only return the order of type `0`.
4. Customer 4 has two orders of type `1`. Since they have no orders of type `0`, we return both orders.

### Thought Process

1. **Identify Customers with Type `0` Orders:** Create a list of `customer_id`s who have at least one order of type `0`. These customers will have all their type `1` orders excluded.
2. **Filter Orders:** Select orders where:
   - The `customer_id` is not in the list of customers with type `0` orders.
   - Or the order itself is of type `0`.
3. Use a Common Table Expression (CTE) to simplify identifying customers with type `0` orders.
4. Combine results by applying the filtering logic in the main query.

### SQL Query

```sql
WITH customer0type AS (
    SELECT customer_id
    FROM orders 
    WHERE order_type = 0
)

SELECT *
FROM orders
WHERE customer_id NOT IN (SELECT customer_id FROM customer0type)
   OR order_type = 0;
```

### Explanation of the Query

1. **CTE `customer0type`:**
   - Selects distinct `customer_id` values where the `order_type` is `0`.
   - This identifies customers who have at least one type `0` order.

2. **Main Query:**
   - Filters all rows in the `orders` table.
   - Includes orders where:
     - The `customer_id` is not in the list of customers with type `0` orders (these customers only have type `1` orders).
     - Or the order itself is of type `0` (even if the customer has type `1` orders, only type `0` orders are included).

### Key Points
- The query ensures no duplicate rows are included.
- Customers with only type `1` orders are fully included in the result.
- The use of `NOT IN` and `OR` guarantees the filtering logic aligns with the problem requirements.

### Output Validation
The output meets the specified criteria, as shown in the example, and can be returned in any order. This ensures correctness while maintaining query simplicity.
