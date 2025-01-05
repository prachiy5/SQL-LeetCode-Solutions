### Capital Gain/Loss
We are given a table:

#### Table: `Stocks`

| Column Name   | Type    |
|---------------|---------|
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |

- `(stock_name, operation_day)` is the primary key.
- `operation` is an ENUM with two possible values: `Buy` and `Sell`.
- Each row in this table indicates that the stock with the given `stock_name` had an operation (`Buy` or `Sell`) on the given `operation_day` at the specified `price`.
- It is guaranteed that:
  - Every `Sell` operation has a corresponding `Buy` operation on a previous day.
  - Every `Buy` operation has a corresponding `Sell` operation on an upcoming day.

### Task
Write a SQL query to compute the **capital gain/loss** for each stock. The capital gain/loss is calculated as the total profit or loss after all `Buy` and `Sell` operations for a stock.

Return the result in any order.

### Example
#### Input:
**Stocks table:**

| stock_name    | operation | operation_day | price  |
|---------------|-----------|---------------|--------|
| Leetcode      | Buy       | 1             | 1000   |
| Corona Masks  | Buy       | 2             | 10     |
| Leetcode      | Sell      | 5             | 9000   |
| Handbags      | Buy       | 17            | 30000  |
| Corona Masks  | Sell      | 3             | 1010   |
| Corona Masks  | Buy       | 4             | 1000   |
| Corona Masks  | Sell      | 5             | 500    |
| Corona Masks  | Buy       | 6             | 1000   |
| Handbags      | Sell      | 29            | 7000   |
| Corona Masks  | Sell      | 10            | 10000  |

#### Output:

| stock_name    | capital_gain_loss |
|---------------|-------------------|
| Corona Masks  | 9500              |
| Leetcode      | 8000              |
| Handbags      | -23000            |

#### Explanation:
- **Leetcode:**
  - Bought at day 1 for $1000, sold at day 5 for $9000.
  - Capital gain: `9000 - 1000 = 8000`.
- **Handbags:**
  - Bought at day 17 for $30000, sold at day 29 for $7000.
  - Capital loss: `7000 - 30000 = -23000`.
- **Corona Masks:**
  - Bought at day 2 for $10, sold at day 3 for $1010. Gain: `1010 - 10 = 1000`.
  - Bought at day 4 for $1000, sold at day 5 for $500. Loss: `500 - 1000 = -500`.
  - Bought at day 6 for $1000, sold at day 10 for $10000. Gain: `10000 - 1000 = 9000`.
  - Total: `1000 - 500 + 9000 = 9500`.

---

### Thought Process
1. **Calculate Capital Gain/Loss:**
   - For each stock, calculate the sum of profits/losses from all `Buy` and `Sell` operations.
   - Add the `Sell` prices and subtract the `Buy` prices to compute the net gain/loss.

2. **Grouping by Stock Name:**
   - Group by `stock_name` to calculate the total gain/loss for each stock.

3. **Aggregate Results:**
   - Use `SUM()` with a `CASE` statement to conditionally add or subtract the `price` based on the `operation` (`Buy` or `Sell`).

4. **Result Order:**
   - Return the result in any order as specified.

---

### SQL Query
```sql
SELECT 
    stock_name,
    SUM(CASE 
            WHEN operation = 'Sell' THEN price
            WHEN operation = 'Buy' THEN -price
            ELSE 0 
        END) AS capital_gain_loss
FROM stocks
GROUP BY stock_name;
```

---

### Key Takeaways
1. **Conditional Aggregation:**
   - Use `CASE` statements inside `SUM()` to calculate net values based on conditions.

2. **Grouping:**
   - Use `GROUP BY` to compute aggregates for each `stock_name`.

3. **Simplified Calculation:**
   - The logic is straightforward due to the guarantee that all `Buy` operations have corresponding `Sell` operations and vice versa.

### Related Topics
- SQL Aggregations
- Conditional Logic with CASE Statements
- GROUP BY Clause
