# Unique Orders and Customers per Month
### Problem

#### Table: Orders

| Column Name   | Type    |
|---------------|---------|
| order_id      | int     |
| order_date    | date    |
| customer_id   | int     |
| invoice       | int     |

- `order_id` is the unique identifier for each order.
- The table contains information about orders made by customers, including the date and invoice amount.

### Task

For each unique month, find:
1. The number of unique orders (`order_count`) with `invoice > 20`.
2. The number of unique customers (`customer_count`) with `invoice > 20`.

### Example

#### Input

**Orders** table:

| order_id | order_date | customer_id | invoice |
|----------|------------|-------------|---------|
| 1        | 2020-09-15 | 1           | 30      |
| 2        | 2020-09-17 | 2           | 90      |
| 3        | 2020-10-06 | 3           | 20      |
| 4        | 2020-10-20 | 3           | 21      |
| 5        | 2020-11-10 | 1           | 10      |
| 6        | 2020-11-21 | 2           | 15      |
| 7        | 2020-12-01 | 4           | 55      |
| 8        | 2020-12-03 | 4           | 77      |
| 9        | 2021-01-07 | 3           | 31      |
| 10       | 2021-01-15 | 2           | 20      |

#### Output

| month   | order_count | customer_count |
|---------|-------------|----------------|
| 2020-09 | 2           | 2              |
| 2020-10 | 1           | 1              |
| 2020-12 | 2           | 1              |
| 2021-01 | 1           | 1              |

#### Explanation

1. **2020-09:**
   - Two orders (`order_id = 1, 2`) with `invoice > 20`.
   - Two unique customers (`customer_id = 1, 2`).
2. **2020-10:**
   - Two orders (`order_id = 3, 4`), but only one (`order_id = 4`) with `invoice > 20`.
   - One unique customer (`customer_id = 3`).
3. **2020-11:**
   - Two orders (`order_id = 5, 6`), but both have `invoice <= 20`, so the month is excluded.
4. **2020-12:**
   - Two orders (`order_id = 7, 8`) with `invoice > 20`.
   - One unique customer (`customer_id = 4`).
5. **2021-01:**
   - Two orders (`order_id = 9, 10`), but only one (`order_id = 9`) with `invoice > 20`.
   - One unique customer (`customer_id = 3`).

---

### Thought Process

1. **Filter Relevant Rows:**
   - Only include rows where `invoice > 20`.

2. **Extract Month:**
   - Use `DATE_FORMAT(order_date, '%Y-%m')` to extract the year and month.

3. **Count Unique Orders and Customers:**
   - Use `COUNT(DISTINCT order_id)` to count unique orders.
   - Use `COUNT(DISTINCT customer_id)` to count unique customers.

4. **Group and Aggregate:**
   - Group data by `DATE_FORMAT(order_date, '%Y-%m')`.
   - Aggregate counts for each group.

---

### Solution

```sql
SELECT 
    DATE_FORMAT(order_date, '%Y-%m') AS month,
    COUNT(DISTINCT order_id) AS order_count,
    COUNT(DISTINCT customer_id) AS customer_count
FROM Orders
WHERE invoice > 20
GROUP BY DATE_FORMAT(order_date, '%Y-%m');
```

---

### Solution Explanation

1. **Filtering Rows:**
   - `WHERE invoice > 20` filters rows to include only orders with invoices greater than $20.

2. **Extracting Month:**
   - `DATE_FORMAT(order_date, '%Y-%m')` extracts the year and month from `order_date`.

3. **Counting Unique Values:**
   - `COUNT(DISTINCT order_id)` counts unique orders.
   - `COUNT(DISTINCT customer_id)` counts unique customers.

4. **Grouping and Aggregating:**
   - `GROUP BY DATE_FORMAT(order_date, '%Y-%m')` groups data by each unique month.
   - Aggregated counts are calculated for each group.

---

### Key Takeaways

- Use `DATE_FORMAT` for flexible date grouping.
- Use `DISTINCT` within aggregation functions to count unique values.
- Combine filtering, grouping, and aggregation for comprehensive reporting.

---

### Related Topics

- SQL Aggregation
- Date and Time Functions
- Filtering and Grouping
