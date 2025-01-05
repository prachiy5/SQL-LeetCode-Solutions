### Article Views II
We are given a table:

#### Table: `Views`

| Column Name   | Type    |
|---------------|---------|
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |

- This table may have duplicate rows.
- Each row indicates that a viewer viewed an article (written by some author) on a specific date.
- `author_id` and `viewer_id` being the same indicates that the author viewed their own article.

### Task
Write a SQL query to find all the people (`viewer_id`) who viewed more than one distinct article on the same date. The result should return a column named `id`, sorted by `id` in ascending order.

### Example
#### Input:
**Views table:**

| article_id | author_id | viewer_id | view_date  |
|------------|-----------|-----------|------------|
| 1          | 3         | 5         | 2019-08-01 |
| 3          | 4         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |

#### Output:

| id   |
|------|
| 5    |
| 6    |

#### Explanation:
- `viewer_id = 5` viewed articles `1` and `3` on `2019-08-01`.
- `viewer_id = 6` viewed articles `1` and `2` on `2019-08-02`.
- `viewer_id = 4` viewed only one article (`3`) on `2019-07-21`.
- `viewer_id = 7` viewed only one article (`2`) on `2019-08-01`.
- `viewer_id = 1` viewed only one article (`4`) on `2019-07-22`.

Thus, the result includes `5` and `6`.

---

### Thought Process
1. **Group Data**: Group the rows by `viewer_id` and `view_date` to analyze each viewer's activity on a specific date.
2. **Count Distinct Articles**: Use `COUNT(DISTINCT article_id)` to determine the number of distinct articles viewed by each `viewer_id` on each `view_date`.
3. **Filter for Multiple Articles**: Use `HAVING` to filter groups where the count of distinct articles is greater than 1.
4. **Eliminate Duplicates**: Use `DISTINCT` to ensure the result contains unique `viewer_id` values.
5. **Sort Results**: Order the result by `id` in ascending order.

---

### SQL Query
```sql
SELECT DISTINCT viewer_id AS id
FROM Views
GROUP BY view_date, viewer_id
HAVING COUNT(DISTINCT article_id) > 1
ORDER BY viewer_id;
```

---

### Key Takeaways
1. Use `GROUP BY` to group rows based on relevant attributes (e.g., `view_date`, `viewer_id`).
2. Use `COUNT(DISTINCT column_name)` to count unique values within each group.
3. Use `HAVING` to filter groups based on aggregate conditions.
4. Use `DISTINCT` to ensure the result table does not contain duplicate rows.
5. Always order results when the task specifies a particular sorting requirement.

### Related Topics
- Aggregation with `GROUP BY`
- Filtering with `HAVING`
- Using `DISTINCT` to handle duplicates
- Sorting with `ORDER BY`
