# NPV Queries

### Problem

#### Table: NPV

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| year          | int     |
| npv           | int     |

- `(id, year)` is the primary key of this table.
- This table contains information about the inventory `id`, `year`, and the corresponding net present value (`npv`).

#### Table: Queries

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| year          | int     |

- `(id, year)` is the primary key of this table.
- This table contains queries for specific inventory `id` and `year`.

### Task

For each query in the `Queries` table, find the corresponding `npv` value from the `NPV` table. If a matching `npv` is not found, return `0` as the default value.

Return the result in any order.

### Example

#### Input

**NPV** table:

| id   | year   | npv    |
|------|--------|--------|
| 1    | 2018   | 100    |
| 7    | 2020   | 30     |
| 13   | 2019   | 40     |
| 1    | 2019   | 113    |
| 2    | 2008   | 121    |
| 3    | 2009   | 12     |
| 11   | 2020   | 99     |
| 7    | 2019   | 0      |

**Queries** table:

| id   | year   |
|------|--------|
| 1    | 2019   |
| 2    | 2008   |
| 3    | 2009   |
| 7    | 2018   |
| 7    | 2019   |
| 7    | 2020   |
| 13   | 2019   |

#### Output

| id   | year   | npv    |
|------|--------|--------|
| 1    | 2019   | 113    |
| 2    | 2008   | 121    |
| 3    | 2009   | 12     |
| 7    | 2018   | 0      |
| 7    | 2019   | 0      |
| 7    | 2020   | 30     |
| 13   | 2019   | 40     |

---

### Thought Process

1. **Understand the Relationship:**
   - The `Queries` table lists specific `id` and `year` combinations we need to look up.
   - The `NPV` table contains the `npv` values for some of these combinations.
   - If a combination in `Queries` is not found in `NPV`, the `npv` should default to `0`.

2. **Use RIGHT JOIN:**
   - Start with the `Queries` table and join with the `NPV` table using the `id` and `year` columns.
   - A `RIGHT JOIN` ensures all rows from `Queries` are included, even if no match exists in `NPV`.

3. **Handle Missing Values:**
   - Use `IFNULL` to replace `NULL` values in `npv` with `0`.

4. **Order Results:**
   - Sort by `id` to organize the output for clarity.

---

### Solution

```sql
SELECT 
    q.id, 
    q.year, 
    IFNULL(n.npv, 0) AS npv
FROM NPV n
RIGHT JOIN Queries q
ON n.id = q.id AND n.year = q.year
ORDER BY q.id;
```

---

### Solution Explanation

1. **RIGHT JOIN:**
   - Combines the `Queries` table with the `NPV` table based on matching `id` and `year`.
   - Ensures all rows from `Queries` are included, even if no match is found in `NPV`.

2. **IFNULL Function:**
   - Handles cases where `npv` is `NULL` by replacing it with `0`.

3. **SELECT Statement:**
   - Retrieves `id`, `year`, and the `npv` value for each query.

4. **ORDER BY:**
   - Sorts the result by `id` for better readability.

---

### Key Takeaways

- **RIGHT JOIN:** Ensures all rows from the right table (`Queries`) are included in the result.
- **Handling Null Values:** Use `IFNULL` to replace missing values with a default.
- **Sorting Results:** Order the output for clarity and usability.

---

### Related Topics

- SQL Joins
- Handling NULL Values
- Aggregate and Conditional Functions
