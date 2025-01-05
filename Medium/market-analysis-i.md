### Market Analysis I
We are given three tables:

#### Table: `Users`

| Column Name    | Type    |
|----------------|---------|
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |

- `user_id` is the primary key.
- This table contains information about the users of an online shopping website where users can sell and buy items.

#### Table: `Orders`

| Column Name   | Type |
|---------------|------|
| order_id      | int  |
| order_date    | date |
| item_id       | int  |
| buyer_id      | int  |
| seller_id     | int  |

- `order_id` is the primary key.
- `item_id` is a foreign key to the `Items` table.
- `buyer_id` and `seller_id` are foreign keys to the `Users` table.

#### Table: `Items`

| Column Name   | Type    |
|---------------|---------|
| item_id       | int     |
| item_brand    | varchar |

- `item_id` is the primary key.

### Task
Write a SQL query to find, for each user, their `join_date` and the number of orders they made as a buyer in 2019. The result table should include all users, even those with no orders in 2019. Return the result table with the following columns:
- `buyer_id`
- `join_date`
- `orders_in_2019`

### Example
#### Input:
**Users table:**

| user_id | join_date  | favorite_brand |
|---------|------------|----------------|
| 1       | 2018-01-01 | Lenovo         |
| 2       | 2018-02-09 | Samsung        |
| 3       | 2018-01-19 | LG             |
| 4       | 2018-05-21 | HP             |

**Orders table:**

| order_id | order_date | item_id | buyer_id | seller_id |
|----------|------------|---------|----------|-----------|
| 1        | 2019-08-01 | 4       | 1        | 2         |
| 2        | 2018-08-02 | 2       | 1        | 3         |
| 3        | 2019-08-03 | 3       | 2        | 3         |
| 4        | 2018-08-04 | 1       | 4        | 2         |
| 5        | 2018-08-04 | 1       | 3        | 4         |
| 6        | 2019-08-05 | 2       | 2        | 4         |

**Items table:**

| item_id | item_brand |
|---------|------------|
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |

#### Output:

| buyer_id | join_date  | orders_in_2019 |
|----------|------------|----------------|
| 1        | 2018-01-01 | 1              |
| 2        | 2018-02-09 | 2              |
| 3        | 2018-01-19 | 0              |
| 4        | 2018-05-21 | 0              |

#### Explanation:
- User `1` made one order in 2019.
- User `2` made two orders in 2019.
- Users `3` and `4` did not make any orders in 2019.

---

### Thought Process
1. **Join Users with Orders**: Perform a `LEFT JOIN` between the `Users` table and the `Orders` table on `user_id = buyer_id` to ensure all users are included, even those without orders.
2. **Filter Orders in 2019**: Use a condition `YEAR(order_date) = 2019` to focus only on orders placed in 2019.
3. **Count Orders per User**: Use `COUNT(order_id)` to count the number of orders for each user in 2019. Users without orders will have a count of `0` due to the `LEFT JOIN`.
4. **Group and Return Results**: Group by `user_id` and return the required columns: `buyer_id`, `join_date`, and `orders_in_2019`.

---

### SQL Query
```sql
SELECT 
    u.user_id AS buyer_id, 
    u.join_date, 
    COUNT(o.order_id) AS orders_in_2019
FROM Users u
LEFT JOIN Orders o 
ON u.user_id = o.buyer_id AND YEAR(o.order_date) = 2019
GROUP BY u.user_id, u.join_date;
```

---

### Key Takeaways
1. **LEFT JOIN** ensures all users are included, even if they have no orders.
2. **Conditional Join** filters only orders placed in 2019.
3. **Aggregate with COUNT** calculates the total number of orders for each user.
4. **GROUP BY** groups results by user ID and join date for aggregation.

### Related Topics
- Joins in SQL
- Aggregation Functions
- Filtering with Conditions
- Working with Dates
