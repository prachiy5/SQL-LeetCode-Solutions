### Calculate Orders Within Each Interval

We are given a table:

#### Table: Orders
| Column Name | Type |
|-------------|------|
| minute      | int  |
| order_count | int  |

- `minute` is the primary key.
- Each row represents the minute and the number of orders received during that specific minute.
- The total number of rows will always be a multiple of 6.

#### Task
Write a query to calculate the total orders within each interval, where:
- An interval consists of 6 consecutive minutes.
- Minutes 1 to 6 fall within interval 1, minutes 7 to 12 belong to interval 2, and so forth.

Return the result table containing:
- `interval_no`: The interval number.
- `total_orders`: The total number of orders within that interval.

The result should be ordered by `interval_no` in ascending order.

---

### Example Input/Output

##### Input:
**Orders table:**
| minute | order_count |
|--------|-------------|
| 1      | 0           |
| 2      | 2           |
| 3      | 4           |
| 4      | 6           |
| 5      | 1           |
| 6      | 4           |
| 7      | 1           |
| 8      | 2           |
| 9      | 4           |
| 10     | 1           |
| 11     | 4           |
| 12     | 6           |

##### Output:
| interval_no | total_orders |
|-------------|--------------|
| 1           | 17           |
| 2           | 18           |

##### Explanation:
- **Interval 1 (Minutes 1 to 6):**
  - Total orders = \( 0 + 2 + 4 + 6 + 1 + 4 = 17 \).
- **Interval 2 (Minutes 7 to 12):**
  - Total orders = \( 1 + 2 + 4 + 1 + 4 + 6 = 18 \).

The result is ordered by `interval_no` in ascending order.

---

### Thought Process
1. **Determine Intervals:**
   - Use the `CEIL()` function to divide `minute` by 6 and determine the interval number.
   - For example:
     - Minutes 1 to 6 map to interval 1 (\( \text{CEIL}(1/6), \text{CEIL}(2/6), ... \)).
     - Minutes 7 to 12 map to interval 2 (\( \text{CEIL}(7/6), \text{CEIL}(8/6), ... \)).

2. **Aggregate Orders:**
   - Use `SUM(order_count)` to calculate the total orders for each interval.

3. **Group and Order Results:**
   - Group the results by `interval_no`.
   - Order the results by `interval_no` in ascending order.

---

### Query
```sql
SELECT CEIL(minute / 6) AS interval_no,
       SUM(order_count) AS total_orders
FROM orders
GROUP BY 1
ORDER BY 1;
```

---

### Key Takeaways
- **Interval Calculation:** The `CEIL()` function divides `minute` into 6-minute intervals and maps each minute to the corresponding interval number.
- **Aggregation:** The `SUM()` function calculates the total orders within each interval.
- **Grouping and Ordering:** Grouping by `interval_no` ensures aggregation by intervals, and ordering by `interval_no` arranges the output sequentially.

---

### Related Topics
- SQL Aggregate Functions
- Grouping and Ordering
- Mathematical Functions in SQL (e.g., `CEIL`)
