# Invalid Tweets

### Problem

#### Table: Tweets

| Column Name | Type    |
|-------------|---------|
| tweet_id    | int     |
| content     | varchar |

- `tweet_id` is the primary key column.
- The `content` column consists of characters typed on an American keyboard.

#### Task

A tweet is **invalid** if the number of characters in its `content` exceeds 15. Write a query to find the `tweet_id` of all invalid tweets.

---

### Example

#### Input

**Tweets Table**:

| tweet_id | content                           |
|----------|-----------------------------------|
| 1        | Let us Code                       |
| 2        | More than fifteen chars are here! |

#### Output

| tweet_id |
|----------|
| 2        |

#### Explanation

- Tweet 1: Length = 11 (valid).
- Tweet 2: Length = 33 (invalid, as it exceeds 15 characters).

---

### Thought Process

1. **Understand the Problem**:
   - Use the `LENGTH()` function to count the number of characters in the `content` column.
   - Filter rows where the length is greater than 15.

2. **Return Only Invalid Tweets**:
   - Select `tweet_id` for tweets meeting the invalid condition.

3. **Order of Results**:
   - The output can be returned in any order as per the problem statement.

---

### Solution

```sql
SELECT 
    tweet_id 
FROM 
    Tweets
WHERE 
    LENGTH(content) > 15;
```

---

### Solution Explanation

1. **LENGTH(content):**
   - Calculates the number of characters in the `content` column.

2. **WHERE Clause:**
   - Filters rows where the character count exceeds 15.

3. **SELECT Clause:**
   - Returns the `tweet_id` of the invalid tweets.

4. **Output Order:**
   - The result is returned in any order, as no specific ordering is required.

---

### Key Takeaways

- Use the `LENGTH()` function to measure the length of text in SQL.
- Use the `WHERE` clause to filter rows based on specific conditions.
- Always refer to the problem constraints to decide which rows to include in the result.

---

### Related Topics

- String Functions in SQL
- Filtering Rows with WHERE Clause
