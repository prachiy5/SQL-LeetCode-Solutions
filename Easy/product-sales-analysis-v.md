### Problem Statement:

#### Table: Sales

| Column Name | Type  |
|-------------|-------|
| sale_id     | int   |
| product_id  | int   |
| user_id     | int   |
| quantity    | int   |

- `sale_id` contains unique values.
- `product_id` is a foreign key to the `Product` table.
- Each row of this table shows the ID of the product and the quantity purchased by a user.

#### Table: Product

| Column Name | Type |
|-------------|------|
| product_id  | int  |
| price       | int  |

- `product_id` contains unique values.
- Each row of this table indicates the price of each product.

#### Task:
Write a solution to report the spending of each user.

Return the resulting table ordered by `spending` in descending order. In case of a tie, order them by `user_id` in ascending order.

### Example:

#### Input:

**Sales table:**
| sale_id | product_id | user_id | quantity |
|---------|------------|---------|----------|
| 1       | 1          | 101     | 10       |
| 2       | 2          | 101     | 1        |
| 3       | 3          | 102     | 3        |
| 4       | 3          | 102     | 2        |
| 5       | 2          | 103     | 3        |

**Product table:**
| product_id | price |
|------------|-------|
| 1          | 10    |
| 2          | 25    |
| 3          | 15    |

#### Output:
| user_id | spending |
|---------|----------|
| 101     | 125      |
| 102     | 75       |
| 103     | 75       |

#### Explanation:
- **User 101:** Spent `10 * 10 + 1 * 25 = 125`.
- **User 102:** Spent `3 * 15 + 2 * 15 = 75`.
- **User 103:** Spent `3 * 25 = 75`.
- Users 102 and 103 spent the same amount. Since there is a tie, they are ordered by `user_id` in ascending order. User 101 has the highest spending and is listed first.

---

### Thought Process:

1. **Understand the Problem:**
   - We need to calculate each user's total spending by multiplying the `quantity` of products they purchased by the `price` of those products.
   - Aggregate this spending for each `user_id`.

2. **Identify Relationships:**
   - Use the `product_id` column to join the `Sales` table with the `Product` table to get the `price` of each product.

3. **Perform Calculations:**
   - Multiply `quantity` and `price` to calculate the spending for each sale.
   - Use `SUM` to aggregate spending for each user.

4. **Ordering the Results:**
   - Sort the results by `spending` in descending order.
   - In case of ties, order by `user_id` in ascending order.

5. **Query Design:**
   - Use an `INNER JOIN` to connect the `Sales` and `Product` tables.
   - Group by `user_id` to calculate total spending for each user.
   - Use `ORDER BY` for sorting the results.

---

### Query:

```sql
SELECT 
    s.user_id, 
    SUM(s.quantity * p.price) AS spending
FROM 
    sales s
INNER JOIN 
    product p ON s.product_id = p.product_id
GROUP BY 
    s.user_id
ORDER BY 
    spending DESC, 
    s.user_id ASC;
```

---

### Explanation of the Query:

1. **Join the Tables:**
   - `INNER JOIN` connects the `Sales` and `Product` tables using the common column `product_id`.

2. **Calculate Spending:**
   - Multiply `quantity` (from the `Sales` table) by `price` (from the `Product` table) for each sale.
   - Use `SUM` to aggregate this spending for each `user_id`.

3. **Group Data:**
   - Group by `user_id` to ensure spending is calculated separately for each user.

4. **Order the Results:**
   - Use `ORDER BY spending DESC` to show users with the highest spending first.
   - Use `ORDER BY user_id ASC` to break ties by listing users with lower IDs first.

---

### Why This Query Works:
- It accurately calculates spending by combining relevant data from the `Sales` and `Product` tables.
- It uses grouping and ordering effectively to meet the problem requirements.

---

### Output for the Example:
| user_id | spending |
|---------|----------|
| 101     | 125      |
| 102     | 75       |
| 103     | 75       |

This output is correct based on the given input data.
