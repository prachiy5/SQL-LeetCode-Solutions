# Market Analysis II

We have the following tables:

### Users Table:
| Column Name    | Type    |
|----------------|---------|
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |

- **user_id** is the primary key of this table.
- This table contains information about the users of an online shopping website, including their join date and favorite brand.

### Orders Table:
| Column Name   | Type    |
|---------------|---------|
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |

- **order_id** is the primary key of this table.
- This table contains information about the orders made on the platform, including who bought and sold items on a specific date.
- **item_id** is a foreign key referencing the **Items** table.
- **buyer_id** and **seller_id** are foreign keys referencing the **Users** table.

### Items Table:
| Column Name   | Type    |
|---------------|---------|
| item_id       | int     |
| item_brand    | varchar |

- **item_id** is the primary key of this table.
- This table contains information about the items, including their brand.

### Objective
For each user, determine whether the brand of the **second item they sold** matches their favorite brand. If a user sold less than two items, report the result as `no`. Return the result in the following format:

| seller_id | 2nd_item_fav_brand |
|-----------|--------------------|
| 1         | no                 |
| 2         | yes                |
| 3         | yes                |
| 4         | no                 |

### Constraints
- It is guaranteed that no seller sells more than one item on the same day.
- The output should include all users, even if they have not sold any items.

---

# Thought Process

1. **Identify the items sold by each seller:**
   - Join the `Orders` table with the `Items` and `Users` tables to retrieve the `item_brand` and `favorite_brand` for each seller.

2. **Rank the items sold by each seller:**
   - Use the `DENSE_RANK()` window function to rank the items sold by each seller based on the `order_date`.

3. **Filter for the second sold item:**
   - Retrieve rows where the rank is equal to `2`.

4. **Match the brand with the favorite brand:**
   - Compare the `item_brand` of the second sold item with the seller's `favorite_brand`.

5. **Handle users with fewer than two sales:**
   - Use a `LEFT JOIN` with the `Users` table to include all users, even those with no sales or fewer than two sales.

6. **Output the results:**
   - Use a `CASE` statement to output `yes` if the brands match and `no` otherwise.

---

# Query

```sql
WITH second_sold_item AS (
    SELECT 
        o.seller_id, 
        o.item_id, 
        i.item_brand, 
        u.favorite_brand,
        DENSE_RANK() OVER (
            PARTITION BY o.seller_id 
            ORDER BY o.order_date
        ) AS ranking
    FROM Orders o 
    LEFT JOIN Items i ON o.item_id = i.item_id
    LEFT JOIN Users u ON u.user_id = o.seller_id
),

result AS (
    SELECT 
        u.user_id AS seller_id,
        CASE 
            WHEN ssi.item_brand = ssi.favorite_brand THEN 'yes'
            ELSE 'no' 
        END AS 2nd_item_fav_brand
    FROM Users u 
    LEFT JOIN second_sold_item ssi 
        ON u.user_id = ssi.seller_id AND ssi.ranking = 2
    GROUP BY u.user_id, ssi.item_brand, ssi.favorite_brand
)

SELECT *
FROM result;
