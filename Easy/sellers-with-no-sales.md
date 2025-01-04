# Sellers with No Sales 

### Problem

#### Tables

**Customer**:

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| customer_name | varchar |

- `customer_id` is the primary key.
- Contains customer information.

**Orders**:

| Column Name   | Type |
|---------------|------|
| order_id      | int  |
| sale_date     | date |
| order_cost    | int  |
| customer_id   | int  |
| seller_id     | int  |

- `order_id` is the primary key.
- Contains information about orders, including date, cost, and seller.

**Seller**:

| Column Name   | Type    |
|---------------|---------|
| seller_id     | int     |
| seller_name   | varchar |

- `seller_id` is the primary key.
- Contains seller information.

#### Task

Find the `seller_name` of sellers who made **no sales in 2020**. Return the result ordered by `seller_name` in ascending order.

### Example

#### Input

**Customer** table:

| customer_id | customer_name |
|-------------|---------------|
| 101         | Alice         |
| 102         | Bob           |
| 103         | Charlie       |

**Orders** table:

| order_id | sale_date  | order_cost | customer_id | seller_id |
|----------|------------|------------|-------------|-----------|
| 1        | 2020-03-01 | 1500       | 101         | 1         |
| 2        | 2020-05-25 | 2400       | 102         | 2         |
| 3        | 2019-05-25 | 800        | 101         | 3         |
| 4        | 2020-09-13 | 1000       | 103         | 2         |
| 5        | 2019-02-11 | 700        | 101         | 2         |

**Seller** table:

| seller_id | seller_name |
|-----------|-------------|
| 1         | Daniel      |
| 2         | Elizabeth   |
| 3         | Frank       |

#### Output

| seller_name |
|-------------|
| Frank       |

#### Explanation

1. **Daniel**:
   - Made a sale in March 2020.
   - Excluded from the result.
2. **Elizabeth**:
   - Made sales in 2020 and 2019.
   - Excluded from the result.
3. **Frank**:
   - Made a sale in 2019 but no sales in 2020.
   - Included in the result.

---

### Thought Process

1. **Join Tables:**
   - Use a `LEFT JOIN` to combine `Seller` and `Orders` tables based on `seller_id`.

2. **Filter Sales in 2020:**
   - Identify sellers with sales in 2020 using `DATE_FORMAT(sale_date, '%Y') = '2020'`.

3. **Exclude Sellers with Sales in 2020:**
   - Use a subquery to exclude sellers appearing in the filtered results.

4. **Order by Seller Name:**
   - Sort the final result by `seller_name` in ascending order.

---

### Solution

```sql
WITH cte AS (
    SELECT 
        seller_name, 
        sale_date, 
        order_cost, 
        s.seller_id
    FROM seller s
    LEFT JOIN orders o 
    ON s.seller_id = o.seller_id
)
SELECT seller_name 
FROM cte 
WHERE seller_id NOT IN (
    SELECT seller_id 
    FROM cte
    WHERE DATE_FORMAT(sale_date, '%Y') = '2020'
)
ORDER BY seller_name;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Combines `Seller` and `Orders` tables.
   - Includes all sellers, even those without orders.

2. **Filter Sales in 2020:**
   - Identify sellers with sales in 2020 using `DATE_FORMAT` to extract the year from `sale_date`.

3. **Exclude Sellers with 2020 Sales:**
   - Use a subquery to exclude `seller_id` values found in the filtered results.

4. **Order Results:**
   - Sort the final list of seller names alphabetically.

---

### Key Takeaways

- Use `LEFT JOIN` to include all rows from one table, even if there are no matching rows in the other table.
- Use `DATE_FORMAT` for flexible date filtering.
- Combine CTEs and subqueries to simplify filtering logic.
- Ensure results are sorted as specified in the task.

---

### Related Topics

- SQL Joins
- Date Functions in SQL
- Subqueries and Filtering
