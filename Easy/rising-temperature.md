# Rising Temperature

### Problem

#### Table: Weather

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| recordDate    | date    |
| temperature   | int     |

- `id` is the column with unique values for this table.
- There are no different rows with the same `recordDate`.
- This table contains information about the temperature on a certain day.

#### Task
Write a solution to find all dates' `id` with higher temperatures compared to the previous day's temperature (yesterday).

Return the result table in any order.

#### Example

**Input:**

`Weather` table:

| id | recordDate | temperature |
|----|------------|-------------|
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

**Output:**

| id |
|----|
| 2  |
| 4  |

**Explanation:**
- On `2015-01-02`, the temperature was higher than the previous day (10 -> 25).
- On `2015-01-04`, the temperature was higher than the previous day (20 -> 30).

---

### Thought Process

To solve this problem, the goal is to compare the temperature of each day with the temperature of the previous day. Here’s how I approached it step by step:

1. **Understand the Requirement:**
   - Identify rows where the `temperature` on a specific day is higher than the `temperature` on the previous day.

2. **Compare Temperatures:**
   - Use a self-join to compare each row in the `Weather` table with the row for the previous day.

3. **Join Condition:**
   - Match rows where the `recordDate` of one row is one day after the `recordDate` of another row.

4. **Filter for Higher Temperatures:**
   - Use the `WHERE` clause to filter rows where the `temperature` of the current day is greater than the `temperature` of the previous day.

5. **Select Required Column:**
   - Return only the `id` of the rows that meet the condition.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT W1.id 
FROM Weather W1 
INNER JOIN Weather W2
ON W1.recordDate = DATE_ADD(W2.recordDate, INTERVAL 1 DAY)
WHERE W1.temperature > W2.temperature;
```

---

### Solution Explanation

- **Self-Join:**
  - The `Weather` table is joined with itself, creating two aliases `W1` and `W2`.
  - This allows us to compare the temperatures of different rows within the same table.

- **Join Condition:**
  - `W1.recordDate = DATE_ADD(W2.recordDate, INTERVAL 1 DAY)` ensures that each row in `W1` is matched with the row in `W2` that represents the previous day.

- **Filter Condition:**
  - `W1.temperature > W2.temperature` ensures that only rows where the current day’s temperature is higher than the previous day’s temperature are included.

- **Select Statement:**
  - The query selects the `id` of rows from `W1` that meet the condition.

---

### Key Takeaways

- **Self-Joins:** Useful for comparing rows within the same table.
- **DATE_ADD Function:** Helps in handling date arithmetic, such as adding or subtracting days.
- **Filtering with WHERE Clause:** Use conditions to target specific rows based on comparison logic.

---

### Related Topics
- Self-Joins
- Date Arithmetic in SQL
- Filtering with WHERE Clause
