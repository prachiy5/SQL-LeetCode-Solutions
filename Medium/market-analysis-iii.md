### Market Analysis III

We are given three tables:

#### Table: Users
| Column Name    | Type    |
|----------------|---------|
| seller_id      | int     |
| join_date      | date    |
| favorite_brand | varchar |

- `seller_id` is the column of unique values.
- Each row contains information about a seller, their join date, and their favorite brand.

#### Table: Items
| Column Name   | Type    |
|---------------|---------|
| item_id       | int     |
| item_brand    | varchar |

- `item_id` is the column of unique values.
- Each row contains information about an item and its brand.

#### Table: Orders
| Column Name   | Type |
|---------------|------|
| order_id      | int  |
| order_date    | date |
| item_id       | int  |
| seller_id     | int  |

- `order_id` is the column of unique values.
- `item_id` is a foreign key to the `Items` table.
- `seller_id` is a foreign key to the `Users` table.
- Each row contains information about an order, including the item and seller involved.

#### Task
Find the seller(s) who have sold the highest number of unique items where the brand of the item is different from their favorite brand. If there are multiple sellers with the same highest count, return all of them. The result should be ordered by `seller_id` in ascending order.

---

### Example Input/Output

##### Input:
**Users table:**
| seller_id | join_date  | favorite_brand |
|-----------|------------|----------------|
| 1         | 2019-01-01 | Lenovo         |
| 2         | 2019-02-09 | Samsung        |
| 3         | 2019-01-19 | LG             |

**Orders table:**
| order_id | order_date | item_id | seller_id |
|----------|------------|---------|-----------|
| 1        | 2019-08-01 | 4       | 2         |
| 2        | 2019-08-02 | 2       | 3         |
| 3        | 2019-08-03 | 3       | 3         |
| 4        | 2019-08-04 | 1       | 2         |
| 5        | 2019-08-04 | 4       | 2         |

**Items table:**
| item_id | item_brand |
|---------|------------|
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |

##### Output:
| seller_id | num_items |
|-----------|-----------|
| 2         | 1         |
| 3         | 1         |

##### Explanation:
- **Seller 2:**
  - Sold items with brands: HP (twice) and Samsung.
  - Favorite brand: Samsung.
  - Unique items with different brands: HP (1 unique item).
- **Seller 3:**
  - Sold items with brands: Lenovo and LG.
  - Favorite brand: LG.
  - Unique items with different brands: Lenovo (1 unique item).
- Both sellers have sold one unique item with a brand different from their favorite brand, so both are included in the result.

---

### Thought Process
1. **Join Tables:**
   - Combine the `Users`, `Orders`, and `Items` tables to associate each seller with the items they sold and their favorite brand.
2. **Filter Non-Favorite Brands:**
   - Retain only those items where the `item_brand` is different from the seller's `favorite_brand`.
3. **Count Unique Items:**
   - Use `COUNT(DISTINCT item_id)` to count the unique items sold by each seller with a brand different from their favorite brand.
4. **Rank Sellers:**
   - Use `DENSE_RANK()` to rank sellers by their count of unique items in descending order.
5. **Select Top Sellers:**
   - Filter for sellers with the highest count of unique items.
6. **Order Results:**
   - Return the result ordered by `seller_id` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT u.seller_id, 
           u.join_date, 
           u.favorite_brand AS f_brand,
           i.item_id, 
           i.item_brand AS i_brand,
           o.order_id, 
           o.order_date AS o_date
    FROM users u
    INNER JOIN orders o
    ON u.seller_id = o.seller_id
    INNER JOIN items i
    ON o.item_id = i.item_id
),

cte2 AS (
    SELECT seller_id, 
           COUNT(DISTINCT item_id) AS num_items
    FROM cte 
    WHERE i_brand != f_brand
    GROUP BY seller_id
),

cte3 AS (
    SELECT seller_id, 
           num_items,
           DENSE_RANK() OVER (ORDER BY num_items DESC) AS rn
    FROM cte2
)

SELECT seller_id, 
       num_items 
FROM cte3 
WHERE rn = 1
ORDER BY seller_id;
```

---

### Key Takeaways
- **Joins and Filtering:** Joining the `Users`, `Orders`, and `Items` tables and filtering by `i_brand != f_brand` isolates the relevant data for analysis.
- **Aggregation:** Using `COUNT(DISTINCT item_id)` ensures unique items are counted for each seller.
- **Ranking:** `DENSE_RANK()` ranks sellers based on their unique item count, allowing for ties.
- **Output:** Returning only the top-ranked sellers ensures the result includes all eligible sellers.

---

### Related Topics
- SQL Joins
- Window Functions (`DENSE_RANK`)
- Aggregate Functions (`COUNT`)
- Conditional Filtering in SQL
