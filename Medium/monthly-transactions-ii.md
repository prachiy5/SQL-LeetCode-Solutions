### Monthly Transactions II
We are given two tables:

#### Table: `Transactions`

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| country     | varchar |
| state       | enum    |
| amount      | int     |
| trans_date  | date    |

- `id` is the primary key.
- The `state` column is an ENUM with values `"approved"` and `"declined"`.
- This table contains information about incoming transactions, including their date, state, and amount.

#### Table: `Chargebacks`

| Column Name | Type |
|-------------|------|
| trans_id    | int  |
| trans_date  | date |

- `trans_id` is a foreign key referencing the `id` column of the `Transactions` table.
- This table contains information about chargebacks associated with previous transactions.

### Task
Write a SQL query to calculate, for each month and country:
1. The number of approved transactions (`approved_count`).
2. The total amount of approved transactions (`approved_amount`).
3. The number of chargebacks (`chargeback_count`).
4. The total amount of chargebacks (`chargeback_amount`).

**Note:**
- Rows with all metrics (`approved_count`, `approved_amount`, `chargeback_count`, and `chargeback_amount`) equal to zero should be excluded from the result.
- Return the result table in any order.

### Example
#### Input:
**Transactions table:**

| id  | country | state    | amount | trans_date |
|-----|---------|----------|--------|------------|
| 101 | US      | approved | 1000   | 2019-05-18 |
| 102 | US      | declined | 2000   | 2019-05-19 |
| 103 | US      | approved | 3000   | 2019-06-10 |
| 104 | US      | declined | 4000   | 2019-06-13 |
| 105 | US      | approved | 5000   | 2019-06-15 |

**Chargebacks table:**

| trans_id | trans_date |
|----------|------------|
| 102      | 2019-05-29 |
| 101      | 2019-06-30 |
| 105      | 2019-09-18 |

#### Output:

| month   | country | approved_count | approved_amount | chargeback_count | chargeback_amount |
|---------|---------|----------------|-----------------|------------------|-------------------|
| 2019-05 | US      | 1              | 1000            | 1                | 2000              |
| 2019-06 | US      | 2              | 8000            | 1                | 1000              |
| 2019-09 | US      | 0              | 0               | 1                | 5000              |

#### Explanation:
- **2019-05 (US):**
  - Approved transactions: `1` (`id=101`), total amount: `1000`.
  - Chargebacks: `1` (`id=102`), total amount: `2000`.
- **2019-06 (US):**
  - Approved transactions: `2` (`id=103`, `id=105`), total amount: `8000`.
  - Chargebacks: `1` (`id=101`), total amount: `1000`.
- **2019-09 (US):**
  - No approved transactions.
  - Chargebacks: `1` (`id=105`), total amount: `5000`.

---

### Thought Process
1. **Chargeback Metrics:**
   - Join the `Chargebacks` table with the `Transactions` table to fetch `country` and `amount` for each chargeback.
   - Group by `month` and `country` to calculate the count and total amount of chargebacks.
2. **Approved Transaction Metrics:**
   - Filter `Transactions` for rows with `state = 'approved'`.
   - Group by `month` and `country` to calculate the count and total amount of approved transactions.
3. **Combine Metrics:**
   - Use `UNION` of two `LEFT JOIN` operations to combine approved transaction and chargeback metrics.
   - Replace `NULL` values with `0` for proper aggregation.
4. **Exclude All-Zero Rows:**
   - The query inherently ensures rows with no valid data are excluded by construction.

---

### SQL Query
```sql
WITH chargeback AS (
    SELECT 
        DATE_FORMAT(c.trans_date, '%Y-%m') AS month, 
        country, 
        COUNT(trans_id) AS chargeback_count,
        SUM(amount) AS chargeback_amount
    FROM chargebacks c
    LEFT JOIN transactions t
    ON c.trans_id = t.id
    GROUP BY 1, 2
),

approved AS (
    SELECT 
        DATE_FORMAT(trans_date, '%Y-%m') AS month, 
        country, 
        COUNT(id) AS approved_count,
        SUM(amount) AS approved_amount
    FROM transactions 
    WHERE state = 'approved'
    GROUP BY 1, 2
)

SELECT month, a.country,
       approved_count,
       approved_amount,
       IFNULL(chargeback_count, 0) AS chargeback_count,
       IFNULL(chargeback_amount, 0) AS chargeback_amount
FROM approved a 
LEFT JOIN chargeback USING (month, country)

UNION

SELECT month, c.country,
       IFNULL(approved_count, 0) AS approved_count,
       IFNULL(approved_amount, 0) AS approved_amount,
       chargeback_count,
       chargeback_amount
FROM chargeback c
LEFT JOIN approved a
USING (month, country);
