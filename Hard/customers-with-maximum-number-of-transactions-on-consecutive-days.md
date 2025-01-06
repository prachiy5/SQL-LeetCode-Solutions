# Customers with Maximum Number of Transactions on Consecutive Days

## Table: Transactions
| Column Name      | Type |
|------------------|------|
| transaction_id   | int  |
| customer_id      | int  |
| transaction_date | date |
| amount           | int  |

- `transaction_id` is the column with unique values of this table.
- Each row contains information about transactions that includes unique (`customer_id`, `transaction_date`) along with the corresponding `customer_id` and `amount`.

## Task
Write a solution to find all `customer_id` who made the **maximum number of transactions on consecutive days**.

Return all `customer_id` with the maximum number of consecutive transactions. Order the result table by `customer_id` in ascending order.

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

### Output

| customer_id |
|-------------|
| 101         |
| 105         |

### Explanation
1. **Customer 101**:
   - Has 3 transactions, all on consecutive days: May 1st, May 2nd, and May 3rd.
2. **Customer 102**:
   - Has 3 transactions, but only 2 are consecutive: May 3rd and May 4th.
3. **Customer 105**:
   - Has 3 transactions, all on consecutive days: May 1st, May 2nd, and May 3rd.
4. The highest number of consecutive transactions is 3, achieved by customer IDs 101 and 105.
5. The result is ordered by `customer_id` in ascending order.

---

# Thought Process

1. **Assign Groups for Consecutive Days**:
   - Use `ROW_NUMBER()` to create a sequence for each `customer_id`, ordered by `transaction_date`.
   - Calculate an "anchor" value by subtracting the row number from the `transaction_date`. This helps group consecutive dates into the same bucket.

2. **Aggregate Consecutive Groups**:
   - Group by `customer_id` and the calculated "anchor" to find consecutive transactions.
   - Count the number of consecutive days for each group.

3. **Find Maximum Consecutive Transactions**:
   - Identify the maximum number of consecutive days across all customers.

4. **Return Results**:
   - Select `customer_id` where the number of consecutive days equals the maximum value.
   - Order the results by `customer_id`.

---

# Solution

```sql
WITH cte AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY transaction_date) AS rn
    FROM Transactions
),

cte2 AS (
    SELECT *,
           DATE_SUB(transaction_date, INTERVAL rn DAY) AS anchor
    FROM cte
),

cte3 AS (
    SELECT customer_id,
           COUNT(anchor) AS num_of_days
    FROM cte2
    GROUP BY customer_id, anchor
)

SELECT customer_id
FROM cte3
WHERE num_of_days = (SELECT MAX(num_of_days) FROM cte3)
ORDER BY customer_id;
```

---

# Solution Explanation

1. **CTE (`cte`)**:
   - Assign a row number to each transaction for a customer, ordered by `transaction_date`.

2. **CTE (`cte2`)**:
   - Calculate an "anchor" value by subtracting the row number from the `transaction_date`. This groups consecutive dates into the same bucket.

3. **CTE (`cte3`)**:
   - For each group (bucket), calculate the count of consecutive days (`num_of_days`).

4. **Final Query**:
   - Find the maximum number of consecutive days.
   - Return `customer_id` where the number of consecutive days equals the maximum value.
   - Order the results by `customer_id`.

---

# Key Takeaways
- Use `ROW_NUMBER()` to sequence rows for each group.
- Date arithmetic helps group consecutive dates effectively.
- Aggregate functions like `COUNT` and subqueries identify key metrics.

---

# Related Topics
- Window Functions (`ROW_NUMBER`)
- Common Table Expressions (CTEs)
- Aggregation (`COUNT`, `MAX`)
