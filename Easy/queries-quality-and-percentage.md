# Query Quality and Percentage

### Problem

#### Table Overview

**Queries**

| Column Name | Type    |
|-------------|---------|
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |

- The table may contain duplicate rows.
- The `position` column values range from 1 to 500.
- The `rating` column values range from 1 to 5.
- A query is considered poor if its `rating` is less than 3.

#### Definitions
1. **Query Quality:**
   - The average of the ratio between `rating` and `position` for a specific `query_name`.

2. **Poor Query Percentage:**
   - The percentage of queries with `rating < 3` for a specific `query_name`.

Both values should be rounded to 2 decimal places.

#### Task
Find each `query_name`, its `quality`, and `poor_query_percentage`.

---

### Example

**Input:**

`Queries` table:

| query_name | result            | position | rating |
|------------|-------------------|----------|--------|
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |

**Output:**

| query_name | quality | poor_query_percentage |
|------------|---------|-----------------------|
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |

**Explanation:**

- **Dog:**
  - Quality = `((5/1) + (5/2) + (1/200)) / 3 = 2.50`
  - Poor Query Percentage = `(1/3) * 100 = 33.33%`

- **Cat:**
  - Quality = `((2/5) + (3/3) + (4/7)) / 3 = 0.66`
  - Poor Query Percentage = `(1/3) * 100 = 33.33%`

---

### Thought Process

1. **Calculate Query Quality:**
   - Use the formula `avg(rating / position)` to compute the quality of each `query_name`.

2. **Calculate Poor Query Percentage:**
   - Use a `CASE` statement to count rows where `rating < 3`.
   - Divide this count by the total number of rows for the `query_name`.
   - Multiply by 100 to get the percentage.

3. **Group by Query Name:**
   - Group results by `query_name` to calculate metrics for each distinct query.

4. **Round Results:**
   - Use the `ROUND()` function to round both values to 2 decimal places.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
  query_name, 
  ROUND(AVG(rating / position), 2) AS quality,
  ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END) * 100 / COUNT(query_name), 2) AS poor_query_percentage
FROM queries
WHERE query_name IS NOT NULL
GROUP BY query_name;
```

---

### Solution Explanation

1. **Filter Null Query Names:**
   - The `WHERE query_name IS NOT NULL` clause ensures no null `query_name` values are processed.

2. **Calculate Quality:**
   - `AVG(rating / position)` computes the average quality for each `query_name`.

3. **Calculate Poor Query Percentage:**
   - `CASE WHEN rating < 3 THEN 1 ELSE 0 END` identifies poor queries.
   - The sum of these is divided by the total count of queries and multiplied by 100 to get the percentage.

4. **Group and Round:**
   - Group by `query_name` to calculate metrics for each query.
   - Use `ROUND()` to round both metrics to 2 decimal places.

---

### Key Takeaways

- **CASE Statements:** Useful for creating conditional logic in queries.
- **Grouping:** Necessary to calculate metrics for distinct categories.
- **Rounding:** Ensures results are formatted as required.

---

### Related Topics
- Aggregation Functions
- Conditional Expressions in SQL
- Formatting Results with ROUND
