# Consecutive Transactions with Increasing Amounts

## Table: Transactions
| Column Name      | Type |
|------------------|------|
| transaction_id   | int  |
| customer_id      | int  |
| transaction_date | date |
| amount           | int  |

- `transaction_id` is the primary key of this table.
- Each row contains information about transactions that includes unique (`customer_id`, `transaction_date`) along with the corresponding `customer_id` and `amount`.

## Task
Write an SQL query to find the customers who have made consecutive transactions with increasing amounts for at least **three consecutive days**. Include the `customer_id`, start date of the consecutive transactions period, and the end date of the consecutive transactions period. There can be multiple such consecutive transactions by a customer.

Return the result table ordered by `customer_id` in ascending order.

---

# Example

### Input

#### Transactions Table:
| transaction_id | customer_id | transaction_date | amount |
|----------------|-------------|------------------|--------|
| 1              | 101         | 2023-05-01       | 100    |
| 2              | 101         | 2023-05-02       | 150    |
| 3              | 101         | 2023-05-03       | 200    |
| 4              | 102         | 2023-05-01       | 50     |
| 5              | 102         | 2023-05-03       | 100    |
| 6              | 102         | 2023-05-04       | 200    |
| 7              | 105         | 2023-05-01       | 100    |
| 8              | 105         | 2023-05-02       | 150    |
| 9              | 105         | 2023-05-03       | 200    |
| 10             | 105         | 2023-05-04       | 300    |
| 11             | 105         | 2023-05-12       | 250    |
| 12             | 105         | 2023-05-13       | 260    |
| 13             | 105         | 2023-05-14       | 270    |

### Output

| customer_id | consecutive_start | consecutive_end |
|-------------|-------------------|-----------------|
| 101         | 2023-05-01        | 2023-05-03      |
| 105         | 2023-05-01        | 2023-05-04      |
| 105         | 2023-05-12        | 2023-05-14      |

### Explanation
1. **Customer 101**:
   - Transactions on May 1st, May 2nd, and May 3rd have increasing amounts: 100 -> 150 -> 200.
   - These transactions form one valid sequence, so we include them in the output.

2. **Customer 102**:
   - There are no three consecutive transactions with increasing amounts. Hence, this customer is not included in the output.

3. **Customer 105**:
   - Two valid sequences exist:
     - May 1st to May 4th (100 -> 150 -> 200 -> 300).
     - May 12th to May 14th (250 -> 260 -> 270).
   - Both sequences are included in the output.

---

# Thought Process

1. **Identify Consecutive Increasing Transactions**:
   - Use a self-join to compare transactions of the same customer on consecutive days (`DATEDIFF = 1`).
   - Ensure the `amount` of the later transaction is greater than the `amount` of the earlier transaction.

2. **Assign Groups for Consecutive Sequences**:
   - Use `ROW_NUMBER()` to create a unique sequence for each customer, ordered by transaction date.
   - Calculate an "anchor" by subtracting the row number from the transaction date. This helps group consecutive dates into the same bucket.

3. **Aggregate Consecutive Groups**:
   - For each group, calculate the start date, the count of transactions, and the end date.
   - Filter groups where the count of consecutive transactions is at least three.

4. **Return Results**:
   - Include `customer_id`, `consecutive_start`, and `consecutive_end`.
   - Order the results by `customer_id`.

---

# Solution

```sql
WITH cte AS (
    SELECT a.customer_id,
           a.transaction_date
    FROM Transactions a
    INNER JOIN Transactions b
    ON a.customer_id = b.customer_id
       AND b.amount > a.amount
       AND DATEDIFF(b.transaction_date, a.transaction_date) = 1
),

cte2 AS (
    SELECT customer_id,
           transaction_date,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY transaction_date) AS rn
    FROM cte
),

cte3 AS (
    SELECT customer_id,
           transaction_date,
           DATE_SUB(transaction_date, INTERVAL rn DAY) AS anchor
    FROM cte2
),

cte4 AS (
    SELECT customer_id,
           MIN(transaction_date) AS start_date,
           COUNT(*) AS cnt
    FROM cte3
    GROUP BY customer_id, anchor
)

SELECT customer_id, 
       start_date AS consecutive_start,
       DATE_ADD(start_date, INTERVAL cnt DAY) AS consecutive_end
FROM cte4
WHERE cnt > 1
ORDER BY customer_id;
```

---

# Solution Explanation

1. **CTE (`cte`)**:
   - Identify consecutive transactions with increasing amounts by comparing each transaction with the next day's transaction for the same customer.

2. **CTE (`cte2`)**:
   - Assign a row number to each transaction for a customer, ordered by transaction date.

3. **CTE (`cte3`)**:
   - Calculate an "anchor" value by subtracting the row number from the transaction date. This groups consecutive dates into the same bucket.

4. **CTE (`cte4`)**:
   - For each group (bucket), calculate the start date, the count of transactions, and the end date.

5. **Final Query**:
   - Filter groups with at least three consecutive transactions.
   - Return the customer ID, start date, and end date of the consecutive transactions.

---

# Key Takeaways
- Use self-joins to compare rows with specific conditions (e.g., consecutive days and increasing amounts).
- `ROW_NUMBER()` and date arithmetic can group consecutive sequences effectively.
- Recursive grouping helps aggregate sequences into meaningful intervals.

---

# Related Topics
- Window Functions (`ROW_NUMBER`)
- Self-Joins
- Common Table Expressions (CTEs)
