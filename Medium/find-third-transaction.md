### Find Third Transaction

We are given a table:

#### Table: Transactions
| Column Name      | Type     |
|------------------|----------|
| user_id          | int      |
| spend            | decimal  |
| transaction_date | datetime |

- `(user_id, transaction_date)` is the unique key.
- Each row contains:
  - `user_id`: The ID of the user.
  - `spend`: The amount spent in the transaction.
  - `transaction_date`: The date and time of the transaction.

#### Task
Write a query to find the third transaction for each user (if they have at least three transactions) where:
- The `spend` on the third transaction is greater than the `spend` on each of the two preceding transactions.

The result table should include:
- `user_id`: The user ID.
- `third_transaction_spend`: The amount spent in the third transaction.
- `third_transaction_date`: The date and time of the third transaction.

Return the result ordered by `user_id` in ascending order.

---

### Example Input/Output

##### Input:
**Transactions table:**
| user_id | spend  | transaction_date    |
|---------|--------|---------------------|
| 1       | 65.56  | 2023-11-18 13:49:42 |
| 1       | 96.0   | 2023-11-30 02:47:26 |
| 1       | 7.44   | 2023-11-02 12:15:23 |
| 1       | 49.78  | 2023-11-12 00:13:46 |
| 2       | 40.89  | 2023-11-21 04:39:15 |
| 2       | 100.44 | 2023-11-20 07:39:34 |
| 3       | 37.33  | 2023-11-03 06:22:02 |
| 3       | 13.89  | 2023-11-11 16:00:14 |
| 3       | 7.0    | 2023-11-29 22:32:36 |

##### Output:
| user_id | third_transaction_spend | third_transaction_date |
|---------|-------------------------|-------------------------|
| 1       | 65.56                   | 2023-11-18 13:49:42    |

##### Explanation:
- **For user_id 1:**
  - Third transaction: 2023-11-18 at 13:49:42 ($65.56).
  - The first two transactions were:
    - $7.44 (2023-11-02 12:15:23)
    - $49.78 (2023-11-12 00:13:46).
  - The third transaction ($65.56) exceeds the amounts of both preceding transactions.
- **For user_id 2:**
  - Only two transactions, so no third transaction exists.
- **For user_id 3:**
  - Third transaction: 2023-11-29 at 22:32:36 ($7.0).
  - Preceding transactions:
    - $37.33 (2023-11-03 06:22:02)
    - $13.89 (2023-11-11 16:00:14).
  - The third transaction ($7.0) does not exceed the amounts of both preceding transactions, so it is excluded.

---

### Thought Process
1. **Rank Transactions by Date:**
   - Use the `DENSE_RANK()` function to rank transactions by `transaction_date` for each user.
2. **Track Preceding Transactions:**
   - Use `LAG()` to get the spend amounts for the first and second preceding transactions for each row.
3. **Filter for Third Transactions:**
   - Select rows where the rank is 3.
   - Ensure the spend for the third transaction is greater than the spend for both preceding transactions.
4. **Output Columns:**
   - Include `user_id`, `spend` (as `third_transaction_spend`), and `transaction_date` (as `third_transaction_date`).
5. **Order Results:**
   - Order the result by `user_id` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT user_id,
           spend,
           transaction_date,
           LAG(spend, 2) OVER (PARTITION BY user_id ORDER BY transaction_date) AS first_txn,
           LAG(spend, 1) OVER (PARTITION BY user_id ORDER BY transaction_date) AS second_txn,
           DENSE_RANK() OVER (PARTITION BY user_id ORDER BY transaction_date) AS r
    FROM transactions
    ORDER BY user_id, transaction_date
)

SELECT user_id,
       spend AS third_transaction_spend,
       transaction_date AS third_transaction_date
FROM cte
WHERE spend > first_txn
  AND spend > second_txn
  AND r = 3;
```

---

### Key Takeaways
- **Window Functions:**
  - `LAG()` helps track spend amounts for preceding transactions.
  - `DENSE_RANK()` identifies the transaction order for each user.
- **Conditional Filtering:**
  - Use conditions to ensure the third transaction exceeds the spend amounts of the first two.
- **Result Formatting:**
  - Only include rows where the conditions are met and format the output as required.
- **Sorting:**
  - Ordering ensures the output meets the specified format.

---

### Related Topics
- SQL Window Functions (`LAG`, `DENSE_RANK`)
- Filtering with Conditions
- Query Optimization for Large Datasets
