### Count Salary Categories

#### Table: Accounts

```plaintext
+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
```
- `account_id` is the primary key (column with unique values) for this table.
- Each row contains information about the monthly income for one bank account.

#### Task

Write a solution to calculate the number of bank accounts for each salary category. The salary categories are:

1. **Low Salary**: All the salaries strictly less than $20,000.
2. **Average Salary**: All the salaries in the inclusive range [$20,000, $50,000].
3. **High Salary**: All the salaries strictly greater than $50,000.

The result table must contain all three categories. If there are no accounts in a category, return `0`.

Return the result table in any order.

#### Example

##### Input

```plaintext
Accounts table:
+------------+--------+
| account_id | income |
+------------+--------+
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |
+------------+--------+
```

##### Output

```plaintext
+----------------+----------------+
| category       | accounts_count |
+----------------+----------------+
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |
+----------------+----------------+
```

##### Explanation

- **Low Salary**: Account `2` has an income less than $20,000.
- **Average Salary**: No accounts fall in the range [$20,000, $50,000].
- **High Salary**: Accounts `3`, `6`, and `8` have incomes greater than $50,000.

---

### Thought Process

1. **Identify Categories**: Define three salary categories based on the income thresholds provided in the problem:
   - `Low Salary` for incomes `< 20000`
   - `Average Salary` for incomes between `20000` and `50000` (inclusive)
   - `High Salary` for incomes `> 50000`

2. **Aggregate Data**:
   - Count the number of accounts for each category using conditional filtering.

3. **Include Missing Categories**:
   - Ensure that all three categories (`Low Salary`, `Average Salary`, `High Salary`) appear in the result, even if their count is `0`.

4. **Output Requirements**:
   - Display the result table with columns `category` and `accounts_count`.
   - Use `UNION` to combine the results for each category.

---

### Query

```sql
SELECT 'Low Salary' AS CATEGORY, COUNT(account_id) AS ACCOUNTS_COUNT
FROM ACCOUNTS 
WHERE income < 20000

UNION

SELECT 'Average Salary' AS CATEGORY, COUNT(account_id) AS ACCOUNTS_COUNT
FROM ACCOUNTS
WHERE INCOME BETWEEN 20000 AND 50000

UNION

SELECT 'High Salary' AS CATEGORY, COUNT(account_id) AS ACCOUNTS_COUNT
FROM ACCOUNTS
WHERE INCOME > 50000;
