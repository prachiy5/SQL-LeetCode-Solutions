# Kid-Friendly Movies Streamed in June 2020

### Problem

#### Table: TVProgram

| Column Name   | Type    |
|---------------|---------|
| program_date  | date    |
| content_id    | int     |
| channel       | varchar |

- `(program_date, content_id)` is the primary key.
- This table contains the schedule of TV programs, including the date, content ID, and channel.

#### Table: Content

| Column Name      | Type    |
|------------------|---------|
| content_id       | varchar |
| title            | varchar |
| Kids_content     | enum    |
| content_type     | varchar |

- `content_id` is the primary key.
- `Kids_content` indicates if the content is for kids (`'Y'` for yes, `'N'` for no).
- `content_type` specifies the category of the content (e.g., movies, series).

### Task

Find the distinct titles of kid-friendly movies that were streamed in June 2020. Return the result in any order.

### Example

#### Input

**TVProgram** table:

| program_date       | content_id | channel     |
|--------------------|------------|-------------|
| 2020-06-10 08:00  | 1          | LC-Channel  |
| 2020-05-11 12:00  | 2          | LC-Channel  |
| 2020-05-12 12:00  | 3          | LC-Channel  |
| 2020-05-13 14:00  | 4          | Disney Ch   |
| 2020-06-18 14:00  | 4          | Disney Ch   |
| 2020-07-15 16:00  | 5          | Disney Ch   |

**Content** table:

| content_id | title          | Kids_content  | content_type  |
|------------|----------------|---------------|---------------|
| 1          | Leetcode Movie | N             | Movies        |
| 2          | Alg. for Kids  | Y             | Series        |
| 3          | Database Sols  | N             | Series        |
| 4          | Aladdin        | Y             | Movies        |
| 5          | Cinderella     | Y             | Movies        |

#### Output

| title        |
|--------------|
| Aladdin      |

#### Explanation

1. "Leetcode Movie" is not for kids.
2. "Alg. for Kids" is not a movie.
3. "Database Sols" is not a movie.
4. "Aladdin" is a kid-friendly movie streamed in June 2020.
5. "Cinderella" is not streamed in June 2020.

---

### Thought Process

1. **Join Tables:**
   - Use an `INNER JOIN` between `TVProgram` and `Content` tables to match `content_id`.

2. **Filter Conditions:**
   - Check that `Kids_content = 'Y'` to include only kid-friendly content.
   - Check that `content_type = 'Movies'` to include only movies.
   - Use `DATE_FORMAT(program_date, '%Y-%m') = '2020-06'` to include programs streamed in June 2020.

3. **Select and Distinct:**
   - Use `DISTINCT` to ensure unique movie titles are returned.

4. **Order of Results:**
   - The problem does not specify ordering, so results can be returned in any order.

---

### Solution

```sql
SELECT DISTINCT title AS TITLE
FROM Content c
INNER JOIN TVProgram tp
ON c.content_id = tp.content_id
WHERE c.Kids_content = 'Y'
  AND DATE_FORMAT(tp.program_date, '%Y-%m') = '2020-06'
  AND c.content_type = 'Movies';
```

---

### Solution Explanation

1. **INNER JOIN:**
   - Combines rows from `TVProgram` and `Content` tables where `content_id` matches.

2. **Filters:**
   - `Kids_content = 'Y'`: Includes only kid-friendly content.
   - `content_type = 'Movies'`: Includes only movies.
   - `DATE_FORMAT(program_date, '%Y-%m') = '2020-06'`: Filters rows for June 2020.

3. **DISTINCT:**
   - Ensures that only unique movie titles are included in the result.

4. **Select Title:**
   - Outputs the distinct titles of the filtered movies.

---

### Key Takeaways

- Use `INNER JOIN` to combine related tables based on a common key.
- Apply multiple filters with `WHERE` to narrow down results.
- Use `DISTINCT` to remove duplicate rows.
- Format dates with `DATE_FORMAT` for flexible filtering.

---

### Related Topics

- SQL Joins
- Filtering Rows
- Date and Time Functions
