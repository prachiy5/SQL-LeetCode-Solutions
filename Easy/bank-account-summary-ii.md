# Bank Account Summary II

### Problem

#### Tables

**Users**:

| Column Name | Type    |
|-------------|---------|
| account     | int     |
| name        | varchar |

- `account` is the primary key.
- Contains user information, including account numbers and names.

**Transactions**:

| Column Name   | Type |
|---------------|------|
| trans_id      | int  |
| account       | int  |
| amount        | int  |
| transacted_on | date |

- `trans_id` is the primary key.
- Contains transaction details, including the account, amount, and date.
- `amount` is positive for money received and negative for money sent.

#### Task

Find the `name` and `balance` of users whose account balance is greater than 10,000. Account balance is the sum of all transaction amounts for that account. Return the results in any order.

### Example

#### Input

**Users** table:

| account | name     |
|---------|----------|
| 900001  | Alice    |
| 900002  | Bob      |
| 900003  | Charlie  |

**Transactions** table:

| trans_id | account | amount | transacted_on |
|----------|---------|--------|---------------|
| 1        | 900001  | 7000   | 2020-08-01    |
| 2        | 900001  | 7000   | 2020-09-01    |
| 3        | 900001  | -3000  | 2020-09-02    |
| 4        | 900002  | 1000   | 2020-09-12    |
| 5        | 900003  | 6000   | 2020-08-07    |
| 6        | 900003  | 6000   | 2020-09-07    |
| 7        | 900003  | -4000  | 2020-09-11    |

#### Output

| name   | balance |
|--------|---------|
| Alice  | 11000   |

#### Explanation

1. **Alice**:
   - Transactions: `7000 + 7000 - 3000 = 11000`
   - Balance: `11000 > 10000` (included).
2. **Bob**:
   - Transactions: `1000`
   - Balance: `1000 ≤ 10000` (excluded).
3. **Charlie**:
   - Transactions: `6000 + 6000 - 4000 = 8000`
   - Balance: `8000 ≤ 10000` (excluded).

---

### Thought Process

1. **Join Tables:**
   - Use a `LEFT JOIN` to combine `Users` and `Transactions` tables based on the `account` column.
   - This ensures all users are included, even if they have no transactions.

2. **Calculate Balance:**
   - Use `SUM(amount)` to compute the total balance for each user.

3. **Filter by Balance:**
   - Use `HAVING` to filter users whose balance exceeds `10000`.

4. **Group Data:**
   - Group results by `name` to calculate balance per user.

---

### Solution

```sql
SELECT 
    name, 
    SUM(amount) AS balance
FROM Users u
LEFT JOIN Transactions t
ON u.account = t.account
GROUP BY name
HAVING SUM(amount) > 10000;
```

---

### Solution Explanation

1. **LEFT JOIN:**
   - Joins `Users` and `Transactions` on `account` to include all users.

2. **SUM(amount):**
   - Computes the total balance for each user by summing the `amount` column.

3. **GROUP BY:**
   - Groups data by `name` to calculate balance for each user.

4. **HAVING Clause:**
   - Filters users whose total balance exceeds `10000`.

5. **SELECT Clause:**
   - Selects the `name` and calculated `balance` for qualifying users.

---

### Key Takeaways

- Use `LEFT JOIN` to include all rows from one table, even if there are no matches in the other.
- Use `SUM` and `GROUP BY` to aggregate data for each group.
- Use `HAVING` to filter aggregated results based on conditions.

---

### Related Topics

- SQL Joins
- Aggregation and Grouping
- Filtering Aggregated Results with HAVING
