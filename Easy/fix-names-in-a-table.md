# Fix Names in a Table

### Problem

#### Table: Users

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| name        | varchar |

- `user_id` is the primary key.
- `name` contains user names with mixed capitalization.

#### Task

Fix the `name` column so that only the first character is uppercase and the rest are lowercase.

### Example

#### Input

**Users** table:

| user_id | name  |
|---------|-------|
| 1       | aLice |
| 2       | bOB   |

#### Output

| user_id | name  |
|---------|-------|
| 1       | Alice |
| 2       | Bob   |

---

### Thought Process

1. **Identify Name Formatting:**
   - Convert the first character to uppercase.
   - Convert all other characters to lowercase.

2. **Use String Functions:**
   - `UPPER(SUBSTRING(name, 1, 1))`: Capitalizes the first character of the name.
   - `LOWER(SUBSTRING(name, 2))`: Converts the rest of the name to lowercase.

3. **Concatenate Results:**
   - Use `CONCAT` to combine the capitalized first character with the rest of the lowercase name.

4. **Order Results:**
   - Return the results ordered by `user_id`.

---

### Solution

```sql
SELECT 
    user_id, 
    CONCAT(
        UPPER(SUBSTRING(name, 1, 1)), 
        LOWER(SUBSTRING(name, 2))
    ) AS name
FROM 
    Users
ORDER BY 
    user_id;
```

---

### Solution Explanation

1. **UPPER(SUBSTRING(name, 1, 1)):**
   - Extracts the first character of the `name` and converts it to uppercase.

2. **LOWER(SUBSTRING(name, 2)):**
   - Extracts all characters starting from the second position and converts them to lowercase.

3. **CONCAT:**
   - Combines the uppercase first character and the lowercase remainder to form the properly capitalized name.

4. **ORDER BY user_id:**
   - Ensures the results are sorted by `user_id`.

---

### Key Takeaways

- Use string functions like `UPPER`, `LOWER`, and `SUBSTRING` for text manipulation in SQL.
- Concatenation functions like `CONCAT` help combine manipulated parts of a string.
- Always order the results as required by the task.

---

### Related Topics

- SQL String Functions
- Text Manipulation in SQL
- Ordering Query Results
