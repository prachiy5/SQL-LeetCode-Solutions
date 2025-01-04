# Article Views I

### Problem

#### Table Overview

**Views**

| Column Name   | Type |
|---------------|------|
| article_id    | int  |
| author_id     | int  |
| viewer_id     | int  |
| view_date     | date |

- The table does not have a primary key and may contain duplicate rows.
- Each row indicates that a `viewer_id` viewed an `article_id` written by an `author_id` on a specific `view_date`.
- If `author_id` equals `viewer_id`, it means the author viewed their own article.

#### Task
Find all the authors who have viewed at least one of their own articles. Return the result sorted by `id` (i.e., `author_id`) in ascending order.

---

### Example

**Input:**

`Views` table:

| article_id | author_id | viewer_id | view_date  |
|------------|-----------|-----------|------------|
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |

**Output:**

| id   |
|------|
| 4    |
| 7    |

**Explanation:**
- `author_id = 4` viewed their own article (row 6 and 7).
- `author_id = 7` viewed their own article (row 3).
- The result is sorted by `id` in ascending order.

---

### Thought Process

1. **Identify Self-Views:**
   - A self-view occurs when `author_id` equals `viewer_id`.

2. **Filter Unique Authors:**
   - Use `DISTINCT` to ensure each `author_id` appears only once in the output.

3. **Sort the Result:**
   - Order the results by `author_id` in ascending order.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT DISTINCT author_id AS id
FROM Views 
WHERE author_id = viewer_id
ORDER BY author_id;
```

---

### Solution Explanation

1. **Filter Self-Views:**
   - The `WHERE` clause filters rows where `author_id` equals `viewer_id`, identifying self-views.

2. **Get Unique Authors:**
   - The `DISTINCT` keyword ensures that each `author_id` appears only once in the result.

3. **Sort the Results:**
   - The `ORDER BY author_id` clause sorts the results by `id` in ascending order.

---

### Key Takeaways

- **Identifying Conditions:** Use `WHERE` to filter rows based on specific conditions (e.g., `author_id = viewer_id`).
- **Ensuring Uniqueness:** Use `DISTINCT` to remove duplicates.
- **Sorting:** Use `ORDER BY` to sort results by a specific column.

---

### Related Topics
- Filtering with WHERE Clause
- Removing Duplicates with DISTINCT
- Sorting Query Results
