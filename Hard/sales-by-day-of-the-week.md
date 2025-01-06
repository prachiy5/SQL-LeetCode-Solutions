### Sales by Day of the Week

Table: `Orders`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| customer_id   | int     |
| order_date    | date    | 
| item_id       | varchar |
| quantity      | int     |
+---------------+---------+
(ordered_id, item_id) is the primary key (combination of columns with unique values) for this table.
```
This table contains information on the orders placed.
`order_date` is the date `item_id` was ordered by the customer with id `customer_id`.

Table: `Items`

```
+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| item_id             | varchar |
| item_name           | varchar |
| item_category       | varchar |
+---------------------+---------+
```
`item_id` is the primary key (column with unique values) for this table.

Write a solution to report how many units in each category have been ordered on each day of the week.

Return the result table ordered by category.

---

### Example Input

**Orders Table:**
```
+------------+--------------+-------------+--------------+-------------+
| order_id   | customer_id  | order_date  | item_id      | quantity    |
+------------+--------------+-------------+--------------+-------------+
| 1          | 1            | 2020-06-01  | 1            | 10          |
| 2          | 1            | 2020-06-08  | 2            | 10          |
| 3          | 2            | 2020-06-02  | 1            | 5           |
| 4          | 3            | 2020-06-03  | 3            | 5           |
| 5          | 4            | 2020-06-04  | 4            | 1           |
| 6          | 4            | 2020-06-05  | 5            | 5           |
| 7          | 5            | 2020-06-05  | 1            | 10          |
| 8          | 5            | 2020-06-14  | 4            | 5           |
| 9          | 5            | 2020-06-21  | 3            | 5           |
+------------+--------------+-------------+--------------+-------------+
```

**Items Table:**
```
+------------+----------------+---------------+
| item_id    | item_name      | item_category |
+------------+----------------+---------------+
| 1          | LC Alg. Book   | Book          |
| 2          | LC DB. Book    | Book          |
| 3          | LC SmarthPhone | Phone         |
| 4          | LC Phone 2020  | Phone         |
| 5          | LC SmartGlass  | Glasses       |
| 6          | LC T-Shirt XL  | T-Shirt       |
+------------+----------------+---------------+
```

### Expected Output

```
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Category   | Monday    | Tuesday   | Wednesday | Thursday  | Friday    | Saturday  | Sunday    |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Book       | 20        | 5         | 0         | 0         | 10        | 0         | 0         |
| Glasses    | 0         | 0         | 0         | 0         | 5         | 0         | 0         |
| Phone      | 0         | 0         | 5         | 1         | 0         | 0         | 10        |
| T-Shirt    | 0         | 0         | 0         | 0         | 0         | 0         | 0         |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
```

---

### Thought Process

1. **Join Orders and Items:** Combine `Orders` and `Items` tables to match item details with the orders.
2. **Group by Category and Day of the Week:** For each `item_category` and day of the week, calculate the total quantity sold.
3. **Pivot the Data:** Transform rows into columns for each day of the week (`Monday`, `Tuesday`, etc.) with aggregated quantities.
4. **Handle Missing Data:** Use `COALESCE` to fill in missing days with `0` for each category.
5. **Order by Category:** Ensure the output is sorted by `item_category`.

---

### Query

```sql
WITH cte AS (
    SELECT 
        item_category, 
        DATE_FORMAT(order_date, '%W') AS day_of_week,
        SUM(quantity) AS total_quantity
    FROM orders o
    RIGHT JOIN items i
    ON o.item_id = i.item_id
    GROUP BY item_category, day_of_week
)

SELECT 
    item_category AS category,
    COALESCE(SUM(CASE WHEN day_of_week = 'Monday' THEN total_quantity ELSE 0 END), 0) AS Monday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Tuesday' THEN total_quantity ELSE 0 END), 0) AS Tuesday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Wednesday' THEN total_quantity ELSE 0 END), 0) AS Wednesday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Thursday' THEN total_quantity ELSE 0 END), 0) AS Thursday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Friday' THEN total_quantity ELSE 0 END), 0) AS Friday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Saturday' THEN total_quantity ELSE 0 END), 0) AS Saturday,
    COALESCE(SUM(CASE WHEN day_of_week = 'Sunday' THEN total_quantity ELSE 0 END), 0) AS Sunday
FROM cte
GROUP BY category
ORDER BY category;
