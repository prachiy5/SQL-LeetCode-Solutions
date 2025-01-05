# Odd and Even Transactions

You are given a table `transactions` with the following structure:

| Column Name      | Type |
|------------------|------|
| transaction_id   | int  |
| amount           | int  |
| transaction_date | date |

The `transaction_id` column uniquely identifies each row in the table. Each row contains the transaction ID, the transaction amount, and the transaction date.

The goal is to find the sum of amounts for odd and even transactions for each day. If there are no odd or even transactions for a specific date, display the sum as `0`. The result should be ordered by `transaction_date` in ascending order.

# Input Example

### transactions table:

| transaction_id | amount | transaction_date |
|----------------|--------|------------------|
| 1              | 150    | 2024-07-01       |
| 2              | 200    | 2024-07-01       |
| 3              | 75     | 2024-07-01       |
| 4              | 300    | 2024-07-02       |
| 5              | 50     | 2024-07-02       |
| 6              | 120    | 2024-07-03       |

# Output Example

| transaction_date | odd_sum | even_sum |
|------------------|---------|----------|
| 2024-07-01       | 75      | 350      |
| 2024-07-02       | 0       | 350      |
| 2024-07-03       | 0       | 120      |

# Explanation

1. For `2024-07-01`:
   - Sum of amounts for odd transactions (`amount % 2 != 0`): `75`.
   - Sum of amounts for even transactions (`amount % 2 = 0`): `150 + 200 = 350`.

2. For `2024-07-02`:
   - Sum of amounts for odd transactions: `0` (no odd amounts).
   - Sum of amounts for even transactions: `300 + 50 = 350`.

3. For `2024-07-03`:
   - Sum of amounts for odd transactions: `0` (no odd amounts).
   - Sum of amounts for even transactions: `120`.

The output table is ordered by `transaction_date` in ascending order.

# Thought Process

To solve this problem:

1. **Group by Date**:
   - Aggregate the data based on `transaction_date`.

2. **Separate Odd and Even Amounts**:
   - Use conditional aggregation to calculate separate sums for odd and even transactions.
   - A transaction is odd if `amount % 2 != 0` and even if `amount % 2 = 0`.

3. **Handle Missing Values**:
   - Use `CASE` statements to assign `0` when no matching values exist for a condition.

4. **Order Results**:
   - Ensure the output is sorted by `transaction_date` in ascending order.

# Query
```sql
SELECT transaction_date,
    SUM(CASE WHEN amount % 2 != 0 THEN amount ELSE 0 END) AS odd_sum,
    SUM(CASE WHEN amount % 2 = 0 THEN amount ELSE 0 END) AS even_sum
FROM transactions
GROUP BY transaction_date
ORDER BY transaction_date;
