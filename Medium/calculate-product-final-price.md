# Problem Description

You are given two tables:

### Products Table
| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| category    | varchar |
| price       | decimal |

- `product_id` is the unique key for this table.
- Each row contains the product's ID, its category, and its price.

### Discounts Table
| Column Name | Type    |
|-------------|---------|
| category    | varchar |
| discount    | int     |

- `category` is the primary key for this table.
- Each row contains a product category and the percentage discount applied to that category (values range from 0 to 100).

### Task
Find the final price of each product after applying the category discount. If a product's category has no associated discount, its price remains unchanged.

### Output Requirements
Return the result table ordered by `product_id` in ascending order.

### Input Example
#### Products Table:
| product_id | category    | price |
|------------|-------------|-------|
| 1          | Electronics | 1000  |
| 2          | Clothing    | 50    |
| 3          | Electronics | 1200  |
| 4          | Home        | 500   |

#### Discounts Table:
| category    | discount |
|-------------|----------|
| Electronics | 10       |
| Clothing    | 20       |

### Output Example
| product_id | final_price | category    |
|------------|-------------|-------------|
| 1          | 900         | Electronics |
| 2          | 40          | Clothing    |
| 3          | 1080        | Electronics |
| 4          | 500         | Home        |

### Explanation
1. **Product 1**:
   - Category: Electronics
   - Discount: 10%
   - Final price: `1000 - (10% of 1000) = 900`

2. **Product 2**:
   - Category: Clothing
   - Discount: 20%
   - Final price: `50 - (20% of 50) = 40`

3. **Product 3**:
   - Category: Electronics
   - Discount: 10%
   - Final price: `1200 - (10% of 1200) = 1080`

4. **Product 4**:
   - Category: Home
   - Discount: None (no entry in the `Discounts` table)
   - Final price: `500`

### Thought Process
1. **Join Tables**:
   - Perform a left join between the `Products` table and the `Discounts` table on the `category` column to get the discount value for each product.
   - Use `IFNULL` to handle cases where no discount exists, assigning a default discount of 0.

2. **Calculate Final Price**:
   - Use the formula: `(100 - discount) * price / 100` to compute the final price after applying the discount.

3. **Order Results**:
   - Sort the output by `product_id` in ascending order.

### Query
```sql
WITH cte AS (
    SELECT 
        product_id,
        p.category,
        price,
        IFNULL(discount, 0) AS discount
    FROM 
        products p
    LEFT JOIN 
        discounts d
    ON 
        p.category = d.category
)

SELECT 
    product_id,
    ((100 - discount) * price) / 100 AS final_price,
    category
FROM 
    cte
ORDER BY 
    product_id;
