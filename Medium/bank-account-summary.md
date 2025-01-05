### Bank Account Summary

Table: Users

```sql
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| user_id      | int     |
| user_name    | varchar |
| credit       | int     |
+--------------+---------+
```

- `user_id` is the primary key (column with unique values) for this table.
- This table contains the current credit information for each user.

Table: Transactions

```sql
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| trans_id      | int     |
| paid_by       | int     |
| paid_to       | int     |
| amount        | int     |
| transacted_on | date    |
+---------------+---------+
```

- `trans_id` is the primary key (column with unique values) for this table.
- Each row of this table contains information about the transaction in the bank.
- User with id (`paid_by`) transfers money to user with id (`paid_to`).

### Task

Leetcode Bank (LCB) helps its coders in making virtual payments. Our bank records all transactions in the `Transactions` table. We want to find out the current balance of all users and check whether they have breached their credit limit (i.e., if their current credit is less than 0).

Write a solution to report:

- `user_id`: The ID of the user.
- `user_name`: The name of the user.
- `credit`: Current balance after performing transactions.
- `credit_limit_breached`: Whether the user has breached their credit limit ("Yes" or "No").

Return the result table in any order.

### Example Input

Table: Users

```sql
+------------+--------------+-------------+
| user_id    | user_name    | credit      |
+------------+--------------+-------------+
| 1          | Moustafa     | 100         |
| 2          | Jonathan     | 200         |
| 3          | Winston      | 10000       |
| 4          | Luis         | 800         | 
+------------+--------------+-------------+
```

Table: Transactions

```sql
+------------+------------+------------+----------+---------------+
| trans_id   | paid_by    | paid_to    | amount   | transacted_on |
+------------+------------+------------+----------+---------------+
| 1          | 1          | 3          | 400      | 2020-08-01    |
| 2          | 3          | 2          | 500      | 2020-08-02    |
| 3          | 2          | 1          | 200      | 2020-08-03    |
+------------+------------+------------+----------+---------------+
```

### Example Output

```sql
+------------+------------+------------+-----------------------+
| user_id    | user_name  | credit     | credit_limit_breached |
+------------+------------+------------+-----------------------+
| 1          | Moustafa   | -100       | Yes                   | 
| 2          | Jonathan   | 500        | No                    |
| 3          | Winston    | 9900       | No                    |
| 4          | Luis       | 800        | No                    |
+------------+------------+------------+-----------------------+
```

### Explanation

- **Moustafa**:
  - Paid $400 on "2020-08-01".
  - Received $200 on "2020-08-03".
  - Credit = 100 - 400 + 200 = -$100.
  - Credit limit breached: Yes.

- **Jonathan**:
  - Received $500 on "2020-08-02".
  - Paid $200 on "2020-08-03".
  - Credit = 200 + 500 - 200 = $500.
  - Credit limit breached: No.

- **Winston**:
  - Received $400 on "2020-08-01".
  - Paid $500 on "2020-08-03".
  - Credit = 10000 + 400 - 500 = $9900.
  - Credit limit breached: No.

- **Luis**:
  - Did not perform any transactions.
  - Credit = $800.
  - Credit limit breached: No.

### Thought Process

1. **Identify Payments and Receipts**:
   - Use the `paid_by` column in the `Transactions` table to calculate the total amount each user has paid.
   - Use the `paid_to` column to calculate the total amount each user has received.

2. **Calculate Current Credit**:
   - Adjust the initial credit of each user using the formula:
     
     ```
     credit = initial_credit - total_payments + total_receipts
     ```

3. **Determine Credit Limit Breach**:
   - If the calculated credit is less than 0, mark the user as having breached the credit limit ("Yes"). Otherwise, mark it as "No".

4. **Join the Results**:
   - Combine the `Users` table with the calculated payments and receipts to derive the final result.

5. **Order the Result**:
   - Return the result in any order.

### SQL Query

```sql
WITH payments AS  (
    SELECT 
        paid_by AS user_id,
        SUM(amount) AS payments
    FROM Transactions
    GROUP BY paid_by
),

received AS (
    SELECT 
        paid_to AS user_id, 
        SUM(amount) AS received
    FROM Transactions
    GROUP BY paid_to
)

SELECT 
    u.user_id, 
    u.user_name, 
    (u.credit - COALESCE(p.payments, 0) + COALESCE(r.received, 0)) AS credit,
    CASE 
        WHEN (u.credit - COALESCE(p.payments, 0) + COALESCE(r.received, 0)) < 0 THEN 'Yes'
        ELSE 'No'
    END AS credit_limit_breached
FROM Users u
LEFT JOIN payments p ON u.user_id = p.user_id
LEFT JOIN received r ON u.user_id = r.user_id;
