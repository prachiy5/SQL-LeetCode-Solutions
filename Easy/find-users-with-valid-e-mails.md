# Find Users with Valid Emails

### Problem

#### Table: Users

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| name          | varchar |
| mail          | varchar |

- `user_id` is the primary key.
- The table contains user information, including their email addresses.

### Task

Find the users with valid emails. A valid email has:
1. A prefix name that:
   - Starts with a letter.
   - May contain letters (upper or lower case), digits, underscores (`_`), periods (`.`), and/or dashes (`-`).
2. A domain of `@leetcode.com`.

Return the result table in any order.

### Example

#### Input

**Users** table:

| user_id | name      | mail                    |
|---------|-----------|-------------------------|
| 1       | Winston   | winston@leetcode.com    |
| 2       | Jonathan  | jonathanisgreat         |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |
| 5       | Marwan    | quarz#2020@leetcode.com |
| 6       | David     | david69@gmail.com       |
| 7       | Shapiro   | .shapo@leetcode.com     |

#### Output

| user_id | name      | mail                    |
|---------|-----------|-------------------------|
| 1       | Winston   | winston@leetcode.com    |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |

#### Explanation

1. User 2's email is invalid because it lacks a domain.
2. User 5's email contains an invalid character (`#`).
3. User 6's email is invalid because it does not have the `@leetcode.com` domain.
4. User 7's email is invalid because it starts with a period.

---

### Thought Process

1. **Understand the Rules for Valid Emails:**
   - The prefix must start with a letter.
   - The prefix can only include letters, digits, underscores, periods, and dashes.
   - The domain must be `@leetcode.com`.

2. **Regular Expression Matching:**
   - Use SQL's `REGEXP` function to match emails against the pattern.
   - Pattern explanation:
     - `^[a-zA-Z]`: Ensures the email starts with a letter.
     - `[a-zA-Z0-9_.-]*`: Allows valid characters (letters, digits, underscores, periods, dashes) in the prefix.
     - `@leetcode[.]com$`: Ensures the domain is `@leetcode.com`.

3. **Filter Valid Emails:**
   - Use the `WHERE` clause with the `REGEXP` function to filter rows matching the pattern.

---

### Solution

```sql
SELECT *
FROM Users
WHERE mail REGEXP '^[a-zA-Z][a-zA-Z0-9_.-]*@leetcode[.]com$';
```

---

### Solution Explanation

1. **REGEXP Pattern:**
   - `^[a-zA-Z]`: The email must start with an alphabetic letter.
   - `[a-zA-Z0-9_.-]*`: The prefix may contain valid characters after the first letter.
   - `@leetcode[.]com$`: The domain must match exactly `@leetcode.com`.

2. **Filtering Rows:**
   - The `WHERE` clause ensures only rows with valid emails are included in the result.

3. **Select All Columns:**
   - The `SELECT *` retrieves all columns from the `Users` table for valid emails.

---

### Key Takeaways

- **Regular Expressions:** Use `REGEXP` to validate patterns in strings.
- **Email Validation:** Ensure both the prefix and domain meet specific rules.
- **Pattern Matching:** Combine anchors (`^`, `$`) and character classes (`[]`) for precise matching.

---

### Related Topics

- String Matching
- SQL Regular Expressions
- Filtering Rows with WHERE
