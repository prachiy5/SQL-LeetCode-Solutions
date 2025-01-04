# Delete Duplicate Emails

### Problem

#### Table: Person

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| email       | varchar |

- `id` is the primary key (column with unique values) for this table.
- Each row of this table contains an email.
- The emails will not contain uppercase letters.

#### Task
Write a solution to delete all duplicate emails from the `Person` table, keeping only one unique email with the smallest `id`.

- For SQL users: Write a `DELETE` statement to remove duplicates.
- After running the script, the result should be the `Person` table with only unique emails.
- The final order of the `Person` table does not matter.

#### Example

**Input:**

`Person` table:

| id | email            |
|----|------------------|
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |

**Output:**

| id | email            |
|----|------------------|
| 1  | john@example.com |
| 2  | bob@example.com  |

**Explanation:**
- `john@example.com` is repeated two times.
- The row with the smallest `id` (id = 1) is kept, and the duplicate (id = 3) is deleted.

---

### Thought Process

To solve this problem, the goal is to remove duplicate emails while retaining the row with the smallest `id` for each email. Here’s how I approached it step by step:

1. **Understand the Requirement:**
   - Identify duplicate emails in the `Person` table.
   - For each duplicate, keep the row with the smallest `id` and delete the others.

2. **Compare Rows:**
   - Use a self-join to compare rows in the table.
   - Match rows where the `email` is the same but the `id` is different.

3. **Filter Duplicates:**
   - Identify rows where the `id` is greater than the smallest `id` for the same email.

4. **Delete Duplicates:**
   - Use the `DELETE` statement to remove rows that meet the above condition.

---

### Solution

```sql
# Write your MySQL query statement below
DELETE p1
FROM Person p1, Person p2
WHERE p1.email = p2.email AND p1.id > p2.id;
```

---

### Solution Explanation

- **Self-Join:**
  - The table `Person` is joined with itself, creating two aliases `p1` and `p2`.
  - This allows us to compare rows in the same table.

- **Join Condition:**
  - `p1.email = p2.email`: Ensures rows have the same email.
  - `p1.id > p2.id`: Ensures we only target rows with a larger `id` (the duplicates).

- **DELETE Statement:**
  - Deletes rows from `p1` that are duplicates of `p2` (same email but larger `id`).

---

### Key Takeaways

- **Self-Join for Duplicates:** Useful when comparing rows within the same table.
- **DELETE with Conditions:** Combine `WHERE` and comparisons to target specific rows for deletion.
- **Smallest ID Retention:** Ensure that only the row with the smallest `id` is kept.

---

### Related Topics
- Self-Joins
- DELETE Statement
- Handling Duplicates in SQL
