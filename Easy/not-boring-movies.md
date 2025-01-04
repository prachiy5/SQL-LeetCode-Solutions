# Not Boring Movies

### Problem

#### Table: Cinema

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| movie       | varchar |
| description | varchar |
| rating      | float   |

- `id` is the primary key.
- Each row contains information about a movie, including its name, genre, and rating.
- `rating` is a 2-decimal-place float in the range `[0, 10]`.

#### Task
Write a query to find all movies that meet the following conditions:
1. Have an odd-numbered `id`.
2. Have a `description` that is not "boring".

The result should be sorted by `rating` in descending order.

---

### Example

**Input:**

`Cinema` table:

| id | movie      | description | rating |
|----|------------|-------------|--------|
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |

**Output:**

| id | movie      | description | rating |
|----|------------|-------------|--------|
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |

**Explanation:**
- Movies with odd-numbered IDs: `1`, `3`, and `5`.
- Movie with ID `3` has a description of "boring", so it is excluded.
- Movies `1` and `5` meet the criteria and are sorted by their ratings in descending order.

---

### Thought Process

1. **Filter by Odd-Numbered ID:**
   - Use the modulo operator (`id % 2 != 0`) to identify odd-numbered IDs.

2. **Exclude Boring Descriptions:**
   - Use the `<>` operator to filter out rows where `description` is "boring".

3. **Sort by Rating:**
   - Use the `ORDER BY` clause to sort the results by `rating` in descending order.

4. **Select Relevant Columns:**
   - Include `id`, `movie`, `description`, and `rating` in the output.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT id, movie, description, rating
FROM Cinema
WHERE id % 2 != 0 
  AND description <> 'boring'
ORDER BY rating DESC;
```

---

### Solution Explanation

1. **WHERE Clause:**
   - Filters rows where `id % 2 != 0` to select odd-numbered IDs.
   - Excludes rows where `description` is "boring" using `<>`.

2. **ORDER BY Clause:**
   - Sorts the filtered rows by `rating` in descending order.

3. **SELECT Statement:**
   - Retrieves the columns `id`, `movie`, `description`, and `rating` for the filtered and sorted rows.

---

### Key Takeaways

- **Modulo Operator (`%`):** Useful for identifying odd or even numbers.
- **Filtering with `<>`:** Use `<>` to exclude specific values.
- **Sorting:** Use `ORDER BY` to control the order of results.

---

### Related Topics
- SQL Filtering with WHERE
- Sorting with ORDER BY
- Using Modulo for Numeric Conditions
