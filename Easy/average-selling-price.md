# Average Selling Price

### Problem

#### Table Overview

**Prices**

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| start_date    | date    |
| end_date      | date    |
| price         | int     |

- `(product_id, start_date, end_date)` is the primary key.
- This table provides the price of each product during a specific time period.
- No two periods for the same product overlap.

**UnitsSold**

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| purchase_date | date    |
| units         | int     |

- This table contains data about the number of units sold for each product on specific dates.

#### Task
Find the **average selling price** for each product:
1. **Average Selling Price** = Total Price of Product / Total Units Sold.
2. If a product does not have any sold units, its **average selling price** is `0`.
3. Round the result to **2 decimal places**.

---

### Example

**Input:**

`Prices` table:

| product_id | start_date | end_date   | price |
|------------|------------|------------|-------|
| 1          | 2019-02-17 | 2019-02-28 | 5     |
| 1          | 2019-03-01 | 2019-03-22 | 20    |
| 2          | 2019-02-01 | 2019-02-20 | 15    |
| 2          | 2019-02-21 | 2019-03-31 | 30    |

`UnitsSold` table:

| product_id | purchase_date | units |
|------------|---------------|-------|
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |

**Output:**

| product_id | average_price |
|------------|---------------|
| 1          | 6.96          |
| 2          | 16.96         |

**Explanation:**

- **Product 1:**
  - **Price Calculation:**
    - For 2019-02-25: `100 * 5 = 500`
    - For 2019-03-01: `15 * 20 = 300`
  - **Average Price:** `(500 + 300) / (100 + 15) = 6.96`

- **Product 2:**
  - **Price Calculation:**
    - For 2019-02-10: `200 * 15 = 3000`
    - For 2019-03-22: `30 * 30 = 900`
  - **Average Price:** `(3000 + 900) / (200 + 30) = 16.96`

---

### Thought Process

1. **Join Tables:**
   - Use a **LEFT JOIN** to combine `Prices` and `UnitsSold` tables.
   - Match rows where `product_id` is the same and `purchase_date` falls within the `start_date` and `end_date` range.

2. **Calculate Total Price:**
   - Multiply `units` by `price` for each matching row.

3. **Calculate Average Price:**
   - Divide the total price by the total units sold for each product.
   - If no units were sold, return `0` as the average price.

4. **Group and Round:**
   - Group results by `product_id` to calculate metrics for each product.
   - Use `ROUND()` to round the average price to 2 decimal places.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
    p.product_id, 
    IFNULL(ROUND(SUM(us.units * p.price) / SUM(us.units), 2), 0) AS average_price
FROM Prices p
LEFT JOIN UnitsSold us 
    ON p.product_id = us.product_id
    AND us.purchase_date BETWEEN p.start_date AND p.end_date
GROUP BY p.product_id;
```

---

### Solution Explanation

1. **Join Tables:**
   - Combine `Prices` and `UnitsSold` using a `LEFT JOIN`.
   - Ensure the `purchase_date` falls between the `start_date` and `end_date` for valid matches.

2. **Calculate Total Price and Units:**
   - `SUM(us.units * p.price)` computes the total price for all sales of a product.
   - `SUM(us.units)` computes the total units sold for the product.

3. **Handle Missing Sales Data:**
   - Use `IFNULL()` to set the `average_price` to `0` if no sales exist for a product.

4. **Group by Product ID:**
   - Group results by `product_id` to calculate metrics for each product.

5. **Round the Result:**
   - Use `ROUND()` to format the `average_price` to 2 decimal places.

---

### Key Takeaways

- **LEFT JOIN:** Ensures that products with no sales are included in the result.
- **Conditional Logic:** Use `IFNULL()` to handle cases with no sales.
- **Precision:** Use `ROUND()` to format numerical results as required.

---

### Related Topics
- SQL Joins and Aggregations
- Handling NULL Values in SQL
- Calculating Weighted Averages in SQL
