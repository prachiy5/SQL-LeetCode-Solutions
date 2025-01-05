### Immediate Food Delivery II
We are given a table:

#### Table: `Delivery`

| Column Name                 | Type |
|-----------------------------|------|
| delivery_id                 | int  |
| customer_id                 | int  |
| order_date                  | date |
| customer_pref_delivery_date | date |

- `delivery_id` is the primary key.
- The table contains information about food deliveries, where customers specify a preferred delivery date, which can either be the same as the order date (immediate orders) or a future date (scheduled orders).

### Definitions
1. **Immediate Order**: An order where `customer_pref_delivery_date = order_date`.
2. **Scheduled Order**: An order where `customer_pref_delivery_date > order_date`.
3. **First Order**: The earliest `order_date` for each customer.

### Task
Write a SQL query to find the percentage of immediate orders among the first orders of all customers, rounded to two decimal places. Return the result in a column named `immediate_percentage`.

### Example
#### Input:
**Delivery table:**

| delivery_id | customer_id | order_date | customer_pref_delivery_date |
|-------------|-------------|------------|-----------------------------|
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-12                  |
| 4           | 3           | 2019-08-24 | 2019-08-24                  |
| 5           | 3           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |
| 7           | 4           | 2019-08-09 | 2019-08-09                  |

#### Output:

| immediate_percentage |
|-----------------------|
| 50.00                |

#### Explanation:
- **Customer 1**: First order is `delivery_id = 1` (scheduled).
- **Customer 2**: First order is `delivery_id = 2` (immediate).
- **Customer 3**: First order is `delivery_id = 5` (scheduled).
- **Customer 4**: First order is `delivery_id = 7` (immediate).

Half of the customers (`2` out of `4`) have immediate first orders, so the result is `50.00%`.

---

### Thought Process
1. **Identify First Orders**: Use a subquery to find the earliest `order_date` for each `customer_id`.
2. **Determine Order Type**: Use a `CASE` statement to classify each first order as either `immediate` or `scheduled` based on the equality of `order_date` and `customer_pref_delivery_date`.
3. **Calculate Percentage**:
   - Count the number of immediate first orders.
   - Divide by the total number of customers and multiply by `100` to get the percentage.
   - Use `ROUND` to format the result to two decimal places.

---

### SQL Query
```sql
WITH cte AS (
    SELECT 
        delivery_id, 
        customer_id, 
        order_date, 
        customer_pref_delivery_date AS cust_pref_date,
        CASE 
            WHEN order_date = customer_pref_delivery_date THEN 'immediate' 
            ELSE 'scheduled' 
        END AS cust_pref_method
    FROM Delivery
    WHERE (customer_id, order_date) IN (
        SELECT customer_id, MIN(order_date)
        FROM Delivery
        GROUP BY customer_id
    )
)

SELECT 
    ROUND(
        COUNT(CASE WHEN cust_pref_method = 'immediate' THEN customer_id END) * 100.0 / COUNT(DISTINCT customer_id), 2
    ) AS immediate_percentage
FROM cte;
```

---

### Key Takeaways
1. Use a **Common Table Expression (CTE)** to simplify identifying the first order for each customer.
2. Classify orders using a `CASE` statement based on their type (`immediate` or `scheduled`).
3. Calculate percentages with `COUNT` and format the result using `ROUND`.
4. Handle grouping efficiently using a combination of subqueries and aggregation.

### Related Topics
- Subqueries and CTEs
- Conditional Aggregation
- Calculating Percentages in SQL
- Window Functions
