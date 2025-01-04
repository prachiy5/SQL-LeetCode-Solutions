# Immediate Food Delivery I

### Problem

#### Table Overview

**Delivery**

| Column Name                 | Type |
|-----------------------------|------|
| delivery_id                 | int  |
| customer_id                 | int  |
| order_date                  | date |
| customer_pref_delivery_date | date |

- `delivery_id` is the primary key.
- The table stores information about food delivery orders, including the date of the order (`order_date`) and the customer's preferred delivery date (`customer_pref_delivery_date`).
- Orders are classified as:
  - **Immediate:** if `order_date = customer_pref_delivery_date`.
  - **Scheduled:** otherwise.

#### Task
Calculate the **percentage of immediate orders** in the table, rounded to 2 decimal places.

---

### Example

**Input:**

`Delivery` table:

| delivery_id | customer_id | order_date | customer_pref_delivery_date |
|-------------|-------------|------------|-----------------------------|
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 5           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-11                  |
| 4           | 3           | 2019-08-24 | 2019-08-26                  |
| 5           | 4           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |

**Output:**

| immediate_percentage |
|-----------------------|
| 33.33                |

**Explanation:**
- Immediate orders: delivery IDs `2` and `3`.
- Total orders: `6`.
- Immediate percentage: `(2 / 6) * 100 = 33.33`.

---

### Thought Process

1. **Classify Orders:**
   - Use a `CASE` statement to classify orders as `immediate` or `scheduled` based on the condition `order_date = customer_pref_delivery_date`.

2. **Count Immediate Orders:**
   - Count the number of `immediate` orders using a filtered query.

3. **Count Total Orders:**
   - Count the total number of orders in the table.

4. **Calculate Percentage:**
   - Divide the count of immediate orders by the total orders, multiply by `100`, and round the result to 2 decimal places.

---

### Solution

```sql
# Write your MySQL query statement below
WITH cte AS (
  SELECT *,
         CASE 
           WHEN order_date = customer_pref_delivery_date THEN 'immediate'
           ELSE 'scheduled'
         END AS order_detail
  FROM Delivery
),

cte2 AS (
  SELECT COUNT(delivery_id) AS immediate_orders
  FROM cte
  WHERE order_detail = 'immediate'
),

cte3 AS (
  SELECT COUNT(delivery_id) AS total
  FROM cte
)

SELECT 
  ROUND((immediate_orders / total) * 100, 2) AS immediate_percentage
FROM cte2, cte3;
```

---

### Solution Explanation

1. **Classify Orders (CTE):**
   - The `cte` Common Table Expression adds a new column `order_detail` that categorizes each order as either `immediate` or `scheduled` based on the comparison `order_date = customer_pref_delivery_date`.

2. **Count Immediate Orders (CTE2):**
   - The `cte2` counts the number of rows where `order_detail = 'immediate'`.

3. **Count Total Orders (CTE3):**
   - The `cte3` counts the total number of rows in the `Delivery` table.

4. **Calculate Percentage:**
   - The final `SELECT` query calculates the percentage of immediate orders using the formula:
     ```
     (immediate_orders / total) * 100
     ```
   - The `ROUND` function ensures the result is rounded to 2 decimal places.

---

### Key Takeaways

- **Classifying Rows:** Use `CASE` statements to derive new columns based on conditions.
- **Combining Counts:** Use multiple CTEs to break down a problem into smaller steps.
- **Percentage Calculation:** Ensure proper rounding for percentages using `ROUND()`.

---

### Related Topics
- Common Table Expressions (CTEs)
- SQL Aggregation Functions
- Rounding in SQL
