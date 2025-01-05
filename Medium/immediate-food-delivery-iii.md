### Immediate Food Delivery III

We are given a table:

#### Table: Delivery
| Column Name                 | Type    |
|-----------------------------|---------|
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |

- `delivery_id` contains unique values.
- Each row contains information about a food delivery order made by a customer, including the order date and the preferred delivery date.
- If the `customer_pref_delivery_date` is the same as the `order_date`, the order is classified as "immediate"; otherwise, it is "scheduled".

#### Task
Write a query to calculate the percentage of immediate orders for each unique `order_date`, rounded to 2 decimal places. The result should be ordered by `order_date` in ascending order.

---

### Example Input/Output

##### Input:
**Delivery table:**
| delivery_id | customer_id | order_date | customer_pref_delivery_date |
|-------------|-------------|------------|-----------------------------|
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-01 | 2019-08-01                  |
| 3           | 1           | 2019-08-01 | 2019-08-01                  |
| 4           | 3           | 2019-08-02 | 2019-08-13                  |
| 5           | 3           | 2019-08-02 | 2019-08-02                  |
| 6           | 2           | 2019-08-02 | 2019-08-02                  |
| 7           | 4           | 2019-08-03 | 2019-08-03                  |
| 8           | 1           | 2019-08-03 | 2019-08-03                  |
| 9           | 5           | 2019-08-04 | 2019-08-08                  |
| 10          | 2           | 2019-08-04 | 2019-08-18                  |

##### Output:
| order_date | immediate_percentage |
|------------|-----------------------|
| 2019-08-01 | 66.67                |
| 2019-08-02 | 66.67                |
| 2019-08-03 | 100.00               |
| 2019-08-04 | 0.00                 |

##### Explanation:
- **2019-08-01:**
  - Total orders: 3
  - Immediate orders: 2
  - Percentage = (2/3)*100 = 66.67 
- **2019-08-02:**
  - Total orders: 3
  - Immediate orders: 2
  - Percentage = (2/3)*100 = 66.67 
- **2019-08-03:**
  - Total orders: 2
  - Immediate orders: 2
  - Percentage = (2/2)*100 = 100.0
- **2019-08-04:**
  - Total orders: 2
  - Immediate orders: 0
  - Percentage = 0/2 = 0.0

---

### Thought Process
1. **Classify Orders:**
   - Use a `CASE` statement to classify each order as either "Immediate" or "Scheduled" based on whether `order_date` matches `customer_pref_delivery_date`.
2. **Calculate Percentages:**
   - For each `order_date`, count the total number of orders.
   - Count the number of "Immediate" orders using a `CASE` statement.
   - Divide the count of "Immediate" orders by the total orders and multiply by 100 to calculate the percentage.
   - Use `ROUND()` to round the percentage to 2 decimal places.
3. **Order Results:**
   - Group by `order_date` to calculate percentages for each date.
   - Order the results by `order_date` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT delivery_id,
           customer_id,
           order_date,
           customer_pref_delivery_date AS customer_pref,
           CASE
               WHEN order_date = customer_pref_delivery_date THEN 'Immediate'
               ELSE 'Scheduled'
           END AS delivery_type
    FROM Delivery
)

SELECT order_date,
       ROUND((COUNT(CASE WHEN delivery_type = 'Immediate' THEN 1 END) / COUNT(order_date)) * 100, 2) AS immediate_percentage
FROM cte
GROUP BY order_date
ORDER BY order_date ASC;
```

---

### Key Takeaways
- **Conditional Aggregation:** Using `CASE` statements inside aggregate functions allows classification and counting of specific subsets of data.
- **CTE Usage:** Common Table Expressions simplify complex queries by breaking them into manageable steps.
- **Rounding:** The `ROUND()` function ensures the percentages are returned with two decimal places for precision.
- **Ordering Results:** Sorting by `order_date` ensures results are returned in the required order.

---

### Related Topics
- SQL Aggregate Functions
- Conditional Logic in SQL
- Common Table Expressions (CTEs)
- SQL Ordering and Grouping
