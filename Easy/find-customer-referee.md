# Find Customer Referee

### Problem

#### Table: Customer

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |
| referee_id  | int     |

- `id` is the primary key column for this table.
- Each row indicates the ID of a customer, their name, and the ID of the customer who referred them.

#### Task
Find the names of customers who were **not referred** by the customer with `id = 2`.

Return the result table in any order.

#### Example

**Input:**

`Customer` table:

| id | name | referee_id |
|----|------|------------|
| 1  | Will | null       |
| 2  | Jane | null       |
| 3  | Alex | 2          |
| 4  | Bill | null       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |

**Output:**

| name |
|------|
| Will |
| Jane |
| Bill |
| Zack |

**Explanation:**
- Alex and Mark are referred by customer with `id = 2`, so they are excluded.
- Will, Jane, Bill, and Zack are either not referred by `id = 2` or have `NULL` in their `referee_id`, so they are included in the output.

---

### Thought Process

To solve this problem, the goal is to filter out customers who were referred by the customer with `id = 2`. Here’s how I approached it step by step:

1. **Understand the Table:**
   - The `referee_id` column tells us who referred each customer.
   - If `referee_id` is `NULL`, it means the customer was not referred by anyone.

2. **Filter by Referee ID:**
   - Exclude rows where `referee_id = 2`.
   - Include rows where `referee_id` is `NULL` or not equal to `2`.

3. **Select Relevant Column:**
   - Return only the `name` of the customers who meet the condition.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT name 
FROM Customer 
WHERE referee_id != 2 OR referee_id IS NULL;
```

---

### Solution Explanation

- **WHERE Clause:**
  - `referee_id != 2`: Includes customers not referred by `id = 2`.
  - `referee_id IS NULL`: Includes customers with no referee.
- **SELECT Statement:**
  - Retrieves only the `name` column of the customers meeting the filter condition.

---

### Key Takeaways

- **Filtering Conditions:** Use `!=` to exclude specific values and `IS NULL` to handle nulls explicitly.
- **Combining Conditions:** Use `OR` to combine multiple conditions in the `WHERE` clause.
- **NULL Values:** Understand how `NULL` behaves in SQL comparisons (it doesn’t equal any value, including itself).

---

### Related Topics
- Filtering Rows in SQL
- Handling NULL Values
- Combining Conditions with OR
