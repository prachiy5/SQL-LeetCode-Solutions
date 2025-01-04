# Find Duplicate Emails

### Problem

#### Table: Person

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| email       | varchar |

- `id` is the primary key (column with unique values) for this table.
- Each row of this table contains an email.
- The emails will not contain uppercase letters, and the `email` field is guaranteed to not be NULL.

#### Task
Write a solution to report all the duplicate emails in the table.

Return the result table in any order.

#### Example

**Input:**

`Person` table:

| id | email   |
|----|---------|
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |

**Output:**

| Email   |
|---------|
| a@b.com |

**Explanation:**
- `a@b.com` is repeated two times in the table.

---

### Thought Process

To solve this problem, the goal is to find emails that appear more than once in the `Person` table. Here’s how I approached it step by step:

1. **Understand the Problem:**
   - Identify emails that are duplicated.
   - Use SQL aggregation to count how many times each email appears.

2. **Grouping Emails:**
   - Use the `GROUP BY` clause to group rows with the same `email`.

3. **Filter for Duplicates:**
   - Use the `HAVING` clause to keep only groups where the count of emails is greater than 1.

4. **Select the Email Column:**
   - Only the `email` column needs to be in the output.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT email
FROM Person
GROUP BY email
HAVING COUNT(email) > 1;
```

---

### Solution Explanation

- **GROUP BY:**
  - Groups rows with the same `email` into one group.
- **COUNT(email):**
  - Counts how many rows are in each group.
- **HAVING COUNT(email) > 1:**
  - Filters out groups with only one email, leaving only duplicate emails.
- **SELECT email:**
  - Returns the `email` column for the filtered groups.

---

### Key Takeaways

- **GROUP BY + HAVING:** This combination is ideal for finding duplicates or performing aggregate filtering.
- **COUNT Function:** Helps determine the number of occurrences of a column's value in the table.
- **HAVING vs WHERE:** `HAVING` is used after grouping, while `WHERE` filters rows before grouping.

---

### Related Topics
- GROUP BY and Aggregation
- Filtering with HAVING Clause
- Counting Duplicates in SQL
