### Suspicious Bank Accounts

**Find Suspicious Bank Accounts**

Given the following database schema:

**Table: Accounts**

```
+----------------+------+ 
| Column Name    | Type |
+----------------+------+ 
| account_id     | int  |
| max_income     | int  |
+----------------+------+
```
- `account_id` is the column with unique values for this table.
- Each row contains information about the maximum monthly income for one bank account.

**Table: Transactions**

```
+----------------+----------+ 
| Column Name    | Type     |
+----------------+----------+ 
| transaction_id | int      |
| account_id     | int      |
| type           | ENUM     |
| amount         | int      |
| day            | datetime |
+----------------+----------+
```
- `transaction_id` is the column with unique values for this table.
- Each row contains information about one transaction.
- `type` is ENUM (category) type of ('Creditor','Debtor'), where:
  - `Creditor` means the user deposited money into their account.
  - `Debtor` means the user withdrew money from their account.
- `amount` is the amount of money deposited/withdrawn during the transaction.

### Problem Statement

A bank account is **suspicious** if the total income exceeds the `max_income` for this account for **two or more consecutive months**. The total income of an account in some month is the sum of all its deposits in that month (transactions of the type `Creditor`).

Write a solution to report the IDs of all suspicious bank accounts.

### Constraints

1. Return the result table in any order.
2. The maximum number of transactions for any account will not exceed 100.

---

### Example Input

**Accounts Table:**

| account_id | max_income |
|------------|------------|
| 3          | 21000      |
| 4          | 10400      |

**Transactions Table:**

| transaction_id | account_id | type     | amount | day                 |
|----------------|------------|----------|--------|---------------------|
| 2              | 3          | Creditor | 107100 | 2021-06-02 11:38:14 |
| 4              | 4          | Creditor | 10400  | 2021-06-20 12:39:18 |
| 11             | 4          | Debtor   | 58800  | 2021-07-23 12:41:55 |
| 1              | 4          | Creditor | 49300  | 2021-05-03 16:11:04 |
| 15             | 3          | Debtor   | 75500  | 2021-05-23 14:40:20 |
| 10             | 3          | Creditor | 102100 | 2021-06-15 10:37:16 |
| 14             | 4          | Creditor | 56300  | 2021-07-21 12:12:25 |
| 19             | 4          | Debtor   | 101100 | 2021-05-09 15:21:49 |
| 8              | 3          | Creditor | 64900  | 2021-07-26 15:09:56 |
| 7              | 3          | Creditor | 90900  | 2021-06-14 11:23:07 |

**Output:**

| account_id |
|------------|
| 3          |

---

### Thought Process

1. **Understand Criteria for Suspicious Accounts:**
   - A suspicious account must have its income exceed its `max_income` for **two consecutive months**.
   - Total income is calculated only for `Creditor` transactions.
2. **Steps to Solve:**
   - Filter `Creditor` transactions.
   - Calculate the total monthly income for each account.
   - Identify months where income exceeds `max_income`.
   - Check for consecutive months with exceeded income.
3. **Use SQL Constructs:**
   - Use `WITH` Common Table Expressions (CTEs) for clarity.
   - Use `LEAD()` or `LAG()` to check consecutive months.
   - Use filtering conditions to find qualifying accounts.

---

### SQL Query

```sql
WITH cte AS (
    SELECT 
        a.account_id,
        SUM(t.amount) AS total_amount,
        a.max_income,
        DATE_FORMAT(t.day, '%Y%m') AS date
    FROM transactions t
    INNER JOIN accounts a 
        ON a.account_id = t.account_id
    WHERE t.type = 'Creditor'
    GROUP BY a.account_id, date
),

cte2 AS (
    SELECT 
        *,
        LEAD(date, 1) OVER (PARTITION BY account_id ORDER BY date) AS next_month
    FROM cte
    WHERE total_amount > max_income
)

SELECT DISTINCT account_id 
FROM cte2
WHERE next_month - date = 1;
