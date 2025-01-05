### Books with NULL Ratings

Table: books

```plaintext
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| book_id        | int     |
| title          | varchar |
| author         | varchar |
| published_year | int     |
| rating         | decimal |
+----------------+---------+
```

- `book_id` is the unique key for this table.
- Each row of this table contains information about a book including its unique ID, title, author, publication year, and rating.
- `rating` can be `NULL`, indicating that the book hasn't been rated yet.

Write a solution to find all books that have not been rated yet (i.e., have a `NULL` rating).

Return the result table ordered by `book_id` in ascending order.

### Example:

#### Input:

**books table:**

```plaintext
+---------+------------------------+------------------+----------------+--------+
| book_id | title                  | author           | published_year | rating |
+---------+------------------------+------------------+----------------+--------+
| 1       | The Great Gatsby       | F. Scott         | 1925           | 4.5    |
| 2       | To Kill a Mockingbird  | Harper Lee       | 1960           | NULL   |
| 3       | Pride and Prejudice    | Jane Austen      | 1813           | 4.8    |
| 4       | The Catcher in the Rye | J.D. Salinger    | 1951           | NULL   |
| 5       | Animal Farm            | George Orwell    | 1945           | 4.2    |
| 6       | Lord of the Flies      | William Golding  | 1954           | NULL   |
+---------+------------------------+------------------+----------------+--------+
```

#### Output:

```plaintext
+---------+------------------------+------------------+----------------+
| book_id | title                  | author           | published_year |
+---------+------------------------+------------------+----------------+
| 2       | To Kill a Mockingbird  | Harper Lee       | 1960           |
| 4       | The Catcher in the Rye | J.D. Salinger    | 1951           |
| 6       | Lord of the Flies      | William Golding  | 1954           |
+---------+------------------------+------------------+----------------+
```

### Explanation:
- The books with `book_id` 2, 4, and 6 have `NULL` ratings.
- These books are included in the result table.
- The other books (with `book_id` 1, 3, and 5) have ratings and are not included.
- The result is ordered by `book_id` in ascending order.

---

### Thought Process:

1. **Understand the Requirement:**
   - We need to filter books with a `NULL` rating.
   - Only specific columns (`book_id`, `title`, `author`, and `published_year`) need to be returned.
   - Results should be ordered by `book_id` in ascending order.

2. **Approach:**
   - Use a `SELECT` query to retrieve the required columns.
   - Use a `WHERE` clause to filter rows where the `rating` is `NULL`.
   - Use `ORDER BY` to sort the results by `book_id` in ascending order.

---

### Query:

```sql
SELECT book_id, title, author, published_year
FROM books
WHERE rating IS NULL
ORDER BY book_id;
```

---

### Explanation of the Query:

1. **Filter Rows with `NULL` Rating:**
   - The `WHERE rating IS NULL` condition ensures only books without a rating are included in the result.

2. **Select Relevant Columns:**
   - `book_id`, `title`, `author`, and `published_year` are selected to match the required output.

3. **Order the Results:**
   - `ORDER BY book_id` sorts the results in ascending order of `book_id`.

---

### Why This Output?
- Books with `NULL` ratings (book_id 2, 4, and 6) are included.
- Columns are restricted to only those required (`book_id`, `title`, `author`, `published_year`).
- Results are sorted by `book_id` as specified.
