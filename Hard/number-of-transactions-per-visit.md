### Number of Transactions per Visit

Table: **Visits**

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| visit_date    | date    |
+---------------+---------+
```
- **user_id** and **visit_date** together form the primary key for this table.
- Each row represents a visit by a user to the bank on a specific date.

Table: **Transactions**

```
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| user_id          | int     |
| transaction_date | date    |
| amount           | int     |
+------------------+---------+
```
- This table contains details of transactions performed by users on specific dates. Multiple rows can exist for the same user on the same date.
- Every **transaction_date** in this table corresponds to a **visit_date** in the **Visits** table.

### Task

You need to calculate the number of users who performed a specific number of transactions during their bank visit. For this:

1. Count the number of transactions (`transactions_count`) for each visit.
2. For each `transactions_count`, count the number of visits (`visits_count`) that had exactly that many transactions.
3. Include all possible values of `transactions_count` from 0 to the maximum observed number of transactions.
4. If no visits exist for a specific `transactions_count`, set `visits_count` to 0.

Return the results ordered by `transactions_count`.

### Example Input

**Visits Table:**
```
+---------+------------+
| user_id | visit_date |
+---------+------------+
| 1       | 2020-01-01 |
| 2       | 2020-01-02 |
| 12      | 2020-01-01 |
| 19      | 2020-01-03 |
| 1       | 2020-01-02 |
| 2       | 2020-01-03 |
| 1       | 2020-01-04 |
| 7       | 2020-01-11 |
| 9       | 2020-01-25 |
| 8       | 2020-01-28 |
+---------+------------+
```

**Transactions Table:**
```
+---------+------------------+--------+
| user_id | transaction_date | amount |
+---------+------------------+--------+
| 1       | 2020-01-02       | 120    |
| 2       | 2020-01-03       | 22     |
| 7       | 2020-01-11       | 232    |
| 1       | 2020-01-04       | 7      |
| 9       | 2020-01-25       | 33     |
| 9       | 2020-01-25       | 66     |
| 8       | 2020-01-28       | 1      |
| 9       | 2020-01-25       | 99     |
+---------+------------------+--------+
```

### Example Output

```
+--------------------+--------------+
| transactions_count | visits_count |
+--------------------+--------------+
| 0                  | 4            |
| 1                  | 5            |
| 2                  | 0            |
| 3                  | 1            |
+--------------------+--------------+
```

### Explanation

- **transactions_count = 0**: Users 1 (2020-01-01), 2 (2020-01-02), 12 (2020-01-01), and 19 (2020-01-03) made no transactions.
- **transactions_count = 1**: Users 2 (2020-01-03), 7 (2020-01-11), 8 (2020-01-28), and 1 (2020-01-02, 2020-01-04) made exactly one transaction.
- **transactions_count = 2**: No users made exactly two transactions.
- **transactions_count = 3**: User 9 (2020-01-25) made exactly three transactions.

### Thought Process

1. **Calculate Transactions per Visit:**
   - Join the `Visits` and `Transactions` tables on `user_id` and `visit_date` (matching with `transaction_date`).
   - Group by `user_id` and `visit_date` to count the number of transactions per visit.

2. **Generate All Possible Counts:**
   - Use the `Transactions` table to determine the maximum number of transactions observed.
   - Generate a list of numbers from 0 to the maximum transactions count.

3. **Combine and Fill Missing Counts:**
   - Perform a `RIGHT JOIN` to include all possible transaction counts, even those with no matching visits.
   - Use `IFNULL` to handle missing data and set `visits_count` to 0 for counts with no visits.

4. **Order Results:**
   - Return the results sorted by `transactions_count` in ascending order.

### Query

```sql
WITH cte AS (
    SELECT v.user_id, 
           visit_date,
           COUNT(t.transaction_date) AS num_transactions
    FROM visits v 
    LEFT JOIN transactions t
    ON v.user_id = t.user_id
    AND v.visit_date = t.transaction_date
    GROUP BY 1, 2
    ORDER BY 1, 2
),

cte3 AS (
    SELECT ROW_NUMBER() OVER() AS rn
    FROM transactions
    UNION 
    SELECT 0
)

SELECT rn AS transactions_count,
       CASE
           WHEN num_transactions IS NULL THEN 0
           ELSE COUNT(*)
       END AS visits_count
FROM cte c 
RIGHT JOIN cte3 ct ON num_transactions = rn
WHERE rn <= (SELECT MAX(num_transactions) FROM cte)
GROUP BY rn
ORDER BY 1;
