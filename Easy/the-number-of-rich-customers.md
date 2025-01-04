# The Number of Rich Customers
### Problem

**Store**
| Column Name | Type |
|-------------|------|
| bill_id     | int  |
| customer_id | int  |
| amount      | int  |

`bill_id` is the primary key (column with unique values) for this table. Each row contains information about the amount of one bill and the customer associated with it.

Write a solution to report the number of customers who had at least one bill with an amount strictly greater than 500.

The result format is in the following example.

**Example 1:**

**Input:**

**Store table:**

| bill_id | customer_id | amount |
|---------|-------------|--------|
| 6       | 1           | 549    |
| 8       | 1           | 834    |
| 4       | 2           | 394    |
| 11      | 3           | 657    |
| 13      | 3           | 257    |

**Output:**

| rich_count |
|------------|
| 2          |

**Explanation:**
- Customer 1 has two bills with amounts strictly greater than 500.
- Customer 2 does not have any bills with an amount strictly greater than 500.
- Customer 3 has one bill with an amount strictly greater than 500.
- Thus, the result is 2 distinct customers.

---

### Thought Process:

1. **Understand the Requirement:**
   - Identify customers who have at least one bill (`amount > 500`).
   - Count the number of distinct customers meeting this condition.

2. **Key Points:**
   - Each `bill_id` corresponds to a specific bill.
   - Customers may have multiple bills, but we count each qualifying customer only once.

3. **Approach:**
   - Use the `WHERE` clause to filter rows where `amount > 500`.
   - Use `DISTINCT` to ensure unique customers are counted.
   - Use `COUNT` to calculate the total number of such customers.

4. **Result:**
   - The query should return a single row with a column `rich_count` indicating the total number of qualifying customers.

---

### Query:

```sql
SELECT COUNT(DISTINCT customer_id) AS rich_count
FROM store
WHERE amount > 500;
```

---

### Explanation of the Query:

1. **Filter Rows:**
   - `WHERE amount > 500` filters bills where the amount is strictly greater than 500.
2. **Find Unique Customers:**
   - `DISTINCT customer_id` ensures that each qualifying customer is counted only once, regardless of the number of bills they have over 500.
3. **Count the Customers:**
   - `COUNT` computes the total number of distinct customers meeting the condition.

---

### Why This Output?

- Customer `1` has two qualifying bills (`549`, `834`).
- Customer `2` has no bills over `500`.
- Customer `3` has one qualifying bill (`657`).
- Total distinct customers with qualifying bills: `2`.

---
