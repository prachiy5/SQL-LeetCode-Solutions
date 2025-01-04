# Customer Order Frequency

### Problem

#### Tables

**Customers**:

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| name          | varchar |
| country       | varchar |

- `customer_id` is the primary key.
- Contains customer information.

**Product**:

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| description   | varchar |
| price         | int     |

- `product_id` is the primary key.
- Contains product information, including price.

**Orders**:

| Column Name   | Type    |
|---------------|---------|
| order_id      | int     |
| customer_id   | int     |
| product_id    | int     |
| order_date    | date    |
| quantity      | int     |

- `order_id` is the primary key.
- Contains order information, including date and quantity.

#### Task

Find the `customer_id` and `name` of customers who spent at least $100 in each of the months **June 2020** and **July 2020**.

### Example

#### Input

**Customers** table:

| customer_id | name      | country  |
|-------------|-----------|----------|
| 1           | Winston   | USA      |
| 2           | Jonathan  | Peru     |
| 3           | Moustafa  | Egypt    |

**Product** table:

| product_id | description   | price |
|------------|---------------|-------|
| 10         | LC Phone      | 300   |
| 20         | LC T-Shirt    | 10    |
| 30         | LC Book       | 45    |
| 40         | LC Keychain   | 2     |

**Orders** table:

| order_id | customer_id | product_id | order_date  | quantity |
|----------|-------------|------------|-------------|----------|
| 1        | 1           | 10         | 2020-06-10  | 1        |
| 2        | 1           | 20         | 2020-07-01  | 1        |
| 3        | 1           | 30         | 2020-07-08  | 2        |
| 4        | 2           | 10         | 2020-06-15  | 2        |
| 5        | 2           | 40         | 2020-07-01  | 10       |
| 6        | 3           | 20         | 2020-06-24  | 2        |
| 7        | 3           | 30         | 2020-06-25  | 2        |
| 9        | 3           | 30         | 2020-05-08  | 3        |

#### Output

| customer_id | name      |
|-------------|-----------|
| 1           | Winston   |

### Thought Process

1. **Join Tables**:
   - Combine `Orders`, `Customers`, and `Product` tables using `INNER JOIN` to access all required information (customer name, product price, and quantity).

2. **Calculate Total Spending**:
   - Compute the total spending for each customer by multiplying `price * quantity` for each order.
   - Group spending by `customer_id` and month.

3. **Filter by Month and Spending**:
   - Identify customers who spent at least $100 in both June 2020 and July 2020.

4. **Ensure Both Months Are Covered**:
   - Use `HAVING COUNT(DISTINCT order_month) = 2` to ensure customers qualify for both months.

---

### Solution

```sql
WITH cte AS (
    SELECT 
        c.customer_id, 
        c.name, 
        p.product_id, 
        p.price, 
        o.order_date, 
        o.quantity
    FROM Orders o
    INNER JOIN Customers c ON o.customer_id = c.customer_id
    INNER JOIN Product p ON p.product_id = o.product_id
    ORDER BY customer_id
),
cte2 AS (
    SELECT 
        customer_id, 
        name, 
        SUM(price * quantity) AS total_amount,
        DATE_FORMAT(order_date, '%Y-%m') AS order_month
    FROM cte
    GROUP BY name, order_month
)
SELECT customer_id, name
FROM cte2
WHERE (total_amount >= 100 AND order_month = '2020-06')
   OR (total_amount >= 100 AND order_month = '2020-07')
GROUP BY customer_id, name
HAVING COUNT(DISTINCT order_month) = 2;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Joins `Orders`, `Customers`, and `Product` tables to consolidate relevant data.

2. **Second CTE:**
   - Groups data by `customer_id` and `order_month`.
   - Calculates the total spending (`SUM(price * quantity)`) for each customer in each month.

3. **Filter Spending:**
   - Checks that spending in June and July 2020 is at least $100 using `WHERE`.

4. **Ensure Two Months:**
   - Uses `HAVING COUNT(DISTINCT order_month) = 2` to ensure the customer qualifies for both months.

---

### Key Takeaways

- Use `WITH` clauses to simplify complex queries.
- Aggregate spending using `SUM` and group by necessary columns.
- Use `HAVING` to filter aggregated results.
- Ensure time-based filtering with `DATE_FORMAT`.

---

### Related Topics

- SQL Joins
- Aggregation and Grouping
- Filtering Results with HAVING
