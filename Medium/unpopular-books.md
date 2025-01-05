### Unpopular Books
We are given two tables:

#### Table: `Books`

| Column Name    | Type    |
|----------------|---------|
| book_id        | int     |
| name           | varchar |
| available_from | date    |

- `book_id` is the primary key.
- This table contains details about books and their availability date.

#### Table: `Orders`

| Column Name    | Type    |
|----------------|---------|
| order_id       | int     |
| book_id        | int     |
| quantity       | int     |
| dispatch_date  | date    |

- `order_id` is the primary key.
- `book_id` is a foreign key referencing the `Books` table.
- This table contains details about orders, including the quantity of books ordered and the dispatch date.

### Task
Write a SQL query to find the books that have sold fewer than 10 copies in the past year (from `2019-06-23`), excluding books that have been available for less than one month from the given date (`2019-06-23`).

Return the result table with columns `book_id` and `name` in any order.

### Example
#### Input:
**Books table:**

| book_id | name               | available_from |
|---------|--------------------|----------------|
| 1       | "Kalila And Demna" | 2010-01-01     |
| 2       | "28 Letters"       | 2012-05-12     |
| 3       | "The Hobbit"       | 2019-06-10     |
| 4       | "13 Reasons Why"   | 2019-06-01     |
| 5       | "The Hunger Games" | 2008-09-21     |

**Orders table:**

| order_id | book_id | quantity | dispatch_date |
|----------|---------|----------|---------------|
| 1        | 1       | 2        | 2018-07-26    |
| 2        | 1       | 1        | 2018-11-05    |
| 3        | 3       | 8        | 2019-06-11    |
| 4        | 4       | 6        | 2019-06-05    |
| 5        | 4       | 5        | 2019-06-20    |
| 6        | 5       | 9        | 2009-02-02    |
| 7        | 5       | 8        | 2010-04-13    |

#### Output:

| book_id | name               |
|---------|--------------------|
| 1       | "Kalila And Demna" |
| 2       | "28 Letters"       |
| 5       | "The Hunger Games" |

#### Explanation:
- "The Hobbit" and "13 Reasons Why" are excluded because they have been available for less than one month.
- "Kalila And Demna", "28 Letters", and "The Hunger Games" sold fewer than 10 copies in the last year.

---

### Thought Process
1. **Filter Books Based on Availability**: Exclude books that have been available for less than one month (`available_from >= 2019-05-23`).
2. **Join Tables**: Use a `LEFT JOIN` to combine the `Books` table with the `Orders` table based on `book_id`.
3. **Calculate Sales in the Last Year**: Use a `CASE` statement to calculate the total `quantity` for orders with a `dispatch_date` within the last year (`dispatch_date >= 2018-06-23`).
4. **Group and Filter**: Group by `book_id` and `name` and filter for books with total sales less than 10.

---

### SQL Query
```sql
SELECT 
    b.book_id, 
    b.name
FROM Books b
LEFT JOIN Orders o 
ON b.book_id = o.book_id
WHERE b.available_from < DATE_ADD('2019-06-23', INTERVAL -1 MONTH)
GROUP BY b.book_id, b.name
HAVING SUM(CASE 
             WHEN o.dispatch_date >= DATE_ADD('2019-06-23', INTERVAL -1 YEAR) 
             THEN o.quantity 
             ELSE 0 
           END) < 10;
```

---

### Key Takeaways
1. Use `LEFT JOIN` to ensure all books are included, even if they have no orders.
2. Use a `CASE` statement within `SUM()` to conditionally calculate the total sales.
3. Apply `WHERE` to filter rows before aggregation (e.g., filter books available for less than one month).
4. Use `HAVING` to filter aggregated results (e.g., books with sales < 10).

### Related Topics
- SQL Joins
- Conditional Aggregation
- Filtering with `WHERE` and `HAVING`
