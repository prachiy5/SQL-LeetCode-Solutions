### Account Balance

**Table:** Transactions

```text
+-------------+------+
| Column Name | Type | 
+-------------+------+
| account_id  | int  | 
| day         | date |
| type        | ENUM | 
| amount      | int  |
+-------------+------+
```

Each row contains information about a transaction, including the transaction type, the day it occurred, and the amount. You need to write a query to report the balance of each user after each transaction. Assume the initial balance of each account is 0 and that the balance will never drop below 0 at any point.

### Requirements
- Return the result table sorted by `account_id` and then by `day`.
- The result table should have the following format:



### Example Input

**Transactions Table:**

```text
+------------+------------+----------+--------+
| account_id | day        | type     | amount |
+------------+------------+----------+--------+
| 1          | 2021-11-07 | Deposit  | 2000   |
| 1          | 2021-11-09 | Withdraw | 1000   |
| 1          | 2021-11-11 | Deposit  | 3000   |
| 2          | 2021-12-07 | Deposit  | 7000   |
| 2          | 2021-12-12 | Withdraw | 7000   |
+------------+------------+----------+--------+
```

### Expected Output

```text
+------------+------------+---------+
| account_id | day        | balance |
+------------+------------+---------+
| 1          | 2021-11-07 | 2000    |
| 1          | 2021-11-09 | 1000    |
| 1          | 2021-11-11 | 4000    |
| 2          | 2021-12-07 | 7000    |
| 2          | 2021-12-12 | 0       |
+------------+------------+---------+
```

### Thought Process
1. **Understand the task:** We need to calculate the balance after each transaction for every account.
2. **Initial conditions:** Each account starts with a balance of 0.
3. **Cumulative Calculation:** For each account, calculate the running balance using the following logic:
   - Add the amount for `Deposit` transactions.
   - Subtract the amount for `Withdraw` transactions.
4. **Sorting:** Ensure the result is sorted by `account_id` and `day`.
5. **Tools to Use:** Use a `CASE` statement to handle deposits and withdrawals and a `SUM` window function to compute the running total.

### Query

```sql
SELECT 
    account_id,
    day,
    SUM(
        CASE 
            WHEN type = 'Deposit' THEN amount 
            ELSE -amount 
        END
    ) OVER (PARTITION BY account_id ORDER BY day) AS balance
FROM 
    transactions;
