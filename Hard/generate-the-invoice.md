# Generate the Invoice

## Table: Products
| Column Name | Type |
|-------------|------|
| product_id  | int  |
| price       | int  |

- `product_id` contains unique values.
- Each row in this table shows the ID of a product and the price of one unit.

## Table: Purchases
| Column Name | Type |
|-------------|------|
| invoice_id  | int  |
| product_id  | int  |
| quantity    | int  |

- `(invoice_id, product_id)` is the primary key (combination of columns with unique values) for this table.
- Each row in this table shows the quantity ordered from one product in an invoice.

## Task
Write a solution to show the details of the invoice with the highest price. If two or more invoices have the same price, return the details of the one with the smallest `invoice_id`.

Return the result table in any order.

---

# Example

### Input

#### Products Table:
| product_id | price |
|------------|-------|
| 1          | 100   |
| 2          | 200   |

#### Purchases Table:
| invoice_id | product_id | quantity |
|------------|------------|----------|
| 1          | 1          | 2        |
| 3          | 2          | 1        |
| 2          | 2          | 3        |
| 2          | 1          | 4        |
| 4          | 1          | 10       |

### Output
| product_id | quantity | price |
|------------|----------|-------|
| 2          | 3        | 600   |
| 1          | 4        | 400   |

### Explanation
1. Invoice 1: price = (2 * 100) = $200
2. Invoice 2: price = (4 * 100) + (3 * 200) = $1000
3. Invoice 3: price = (1 * 200) = $200
4. Invoice 4: price = (10 * 100) = $1000

The highest price is $1000, and the invoices with the highest prices are 2 and 4. We return the details of the one with the smallest ID, which is invoice 2.

---

# Thought Process

1. **Calculate Total Price for Each Product in an Invoice**:
   - Join the `Products` and `Purchases` tables to get the price and quantity for each product.
   - Calculate the total price for each product (`price * quantity`).

2. **Find the Invoice with the Highest Price**:
   - Group by `invoice_id` and calculate the total price for each invoice.
   - Use `DENSE_RANK()` to rank invoices based on their total price in descending order.

3. **Select the Invoice with the Smallest ID Among the Highest Priced Ones**:
   - Filter the invoice(s) with the highest price.
   - Among these, select the one with the smallest `invoice_id`.

4. **Get the Product Details for the Selected Invoice**:
   - Retrieve the `product_id`, `quantity`, and `price` for all products in the selected invoice.

---

# Solution

```sql
WITH cte AS (
    SELECT 
        p.product_id,
        quantity,
        invoice_id,
        price * quantity AS price_quantity
    FROM products pr
    INNER JOIN purchases p
    ON p.product_id = pr.product_id
),

cte2 AS (
    SELECT 
        invoice_id, 
        DENSE_RANK() OVER (ORDER BY SUM(price_quantity) DESC) rn
    FROM cte
    GROUP BY invoice_id
),

cte3 AS (
    SELECT invoice_id 
    FROM cte2 
    WHERE rn = 1 
    ORDER BY invoice_id 
    LIMIT 1
)

SELECT 
    product_id,
    quantity,
    price_quantity AS price 
FROM cte
WHERE invoice_id IN (SELECT * FROM cte3);
```

---

# Solution Explanation

1. **First CTE (`cte`)**:
   - Join the `Products` and `Purchases` tables.
   - Calculate the total price (`price * quantity`) for each product in an invoice.

2. **Second CTE (`cte2`)**:
   - Group by `invoice_id` and calculate the total price for each invoice.
   - Use `DENSE_RANK()` to rank invoices based on their total price in descending order.

3. **Third CTE (`cte3`)**:
   - Filter the invoice(s) with the highest price (`rn = 1`).
   - Among these, select the one with the smallest `invoice_id`.

4. **Final Query**:
   - Retrieve the `product_id`, `quantity`, and total price (`price_quantity`) for all products in the selected invoice.

---

# Key Takeaways
- Use `DENSE_RANK()` to handle cases with ties.
- Use `WITH` clauses to break down complex queries into manageable steps.
- Ensure correctness by filtering and ordering as required by the problem statement.

---

# Related Topics
- SQL Joins
- Window Functions (`DENSE_RANK`)
- Common Table Expressions (CTEs)
