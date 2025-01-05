# Customer Purchasing Behavior Analysis

You are given two tables, `Transactions` and `Products`, with the following structures:

### Transactions Table
| Column Name      | Type    |
|------------------|---------|
| transaction_id   | int     |
| customer_id      | int     |
| product_id       | int     |
| transaction_date | date    |
| amount           | decimal |

- `transaction_id` is the unique identifier for this table.
- Each row contains information about a transaction, including the customer ID, product ID, transaction date, and the amount spent.

### Products Table
| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| category    | varchar |
| price       | decimal |

- `product_id` is the unique identifier for this table.
- Each row contains information about a product, including its category and price.

### Task
For each customer, calculate:
1. The total amount spent.
2. The number of transactions.
3. The number of unique product categories purchased.
4. The average amount spent per transaction.
5. The most frequently purchased product category (if there is a tie, choose the one with the most recent transaction).
6. A loyalty score defined as: 
   \[(\text{Number of transactions} \times 10) + (\text{Total amount spent} / 100)\]
   
   - Round `total_amount`, `avg_transaction_amount`, and `loyalty_score` to 2 decimal places.

Return the result table ordered by `loyalty_score` in descending order, then by `customer_id` in ascending order.

### Input Example
#### Transactions Table:
| transaction_id | customer_id | product_id | transaction_date | amount |
|----------------|-------------|------------|------------------|--------|
| 1              | 101         | 1          | 2023-01-01       | 100.00 |
| 2              | 101         | 2          | 2023-01-15       | 150.00 |
| 3              | 102         | 1          | 2023-01-01       | 100.00 |
| 4              | 102         | 3          | 2023-01-22       | 200.00 |
| 5              | 101         | 3          | 2023-02-10       | 200.00 |

#### Products Table:
| product_id | category | price  |
|------------|----------|--------|
| 1          | A        | 100.00 |
| 2          | B        | 150.00 |
| 3          | C        | 200.00 |

### Output Example
| customer_id | total_amount | transaction_count | unique_categories | avg_transaction_amount | top_category | loyalty_score |
|-------------|--------------|-------------------|-------------------|------------------------|--------------|---------------|
| 101         | 450.00       | 3                 | 3                 | 150.00                 | C            | 34.50         |
| 102         | 300.00       | 2                 | 2                 | 150.00                 | C            | 23.00         |

### Explanation
- **For customer 101:**
  - Total amount spent: `100.00 + 150.00 + 200.00 = 450.00`
  - Number of transactions: `3`
  - Unique categories: `A, B, C (3 categories)`
  - Average transaction amount: `450.00 / 3 = 150.00`
  - Top category: `C` (Customer 101 made 1 purchase each in categories A, B, and C. Since the count is the same for all categories, choose the most recent transaction, which is category C on `2023-02-10`).
  - Loyalty score: `(3 * 10) + (450.00 / 100) = 34.50`

- **For customer 102:**
  - Total amount spent: `100.00 + 200.00 = 300.00`
  - Number of transactions: `2`
  - Unique categories: `A, C (2 categories)`
  - Average transaction amount: `300.00 / 2 = 150.00`
  - Top category: `C` (Customer 102 made 1 purchase each in categories A and C. Since the count is the same for both categories, choose the most recent transaction, which is category C on `2023-01-22`).
  - Loyalty score: `(2 * 10) + (300.00 / 100) = 23.00`

### Thought Process
1. **Join Tables**: Combine `Transactions` and `Products` tables on `product_id` to include category information.
2. **Aggregate Metrics**: For each customer:
   - Sum the total amount spent.
   - Count the number of transactions.
   - Count unique product categories.
   - Calculate the average transaction amount.
   - Compute the loyalty score using the formula provided.
3. **Identify Top Category**:
   - Group by customer and category.
   - Count the number of transactions for each category.
   - Resolve ties by picking the category with the most recent transaction.
4. **Combine Results**: Merge the aggregated metrics and top category.
5. **Sort Results**: Order by loyalty score in descending order, then by customer ID in ascending order.

### Query
```sql
WITH joined AS (
    SELECT 
        t.transaction_id,
        t.customer_id,
        p.product_id,
        t.transaction_date,
        t.amount,
        p.category,
        p.price
    FROM 
        transactions t
    INNER JOIN 
        products p ON t.product_id = p.product_id
),

amount_count AS (
    SELECT 
        customer_id,
        ROUND(SUM(amount), 2) AS total_amount,
        COUNT(transaction_id) AS transaction_count,
        COUNT(DISTINCT category) AS unique_categories,
        ROUND(SUM(amount)/COUNT(transaction_id), 2) AS avg_transaction_amount,
        ROUND((COUNT(transaction_id) * 10) + (SUM(amount) / 100), 2) AS loyalty_score
    FROM 
        joined
    GROUP BY 
        customer_id
),

top_category AS (
    SELECT 
        customer_id,
        category,
        DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(*) DESC, MAX(transaction_date) DESC) AS rn
    FROM 
        joined
    GROUP BY 
        customer_id, category
)

SELECT 
    ac.customer_id, 
    ac.total_amount, 
    ac.transaction_count, 
    ac.unique_categories, 
    ac.avg_transaction_amount, 
    tc.category AS top_category, 
    ac.loyalty_score
FROM 
    amount_count ac
LEFT JOIN 
    top_category tc ON ac.customer_id = tc.customer_id
WHERE 
    tc.rn = 1
ORDER BY 
    ac.loyalty_score DESC, ac.customer_id ASC;
