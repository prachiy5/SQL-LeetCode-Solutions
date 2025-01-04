# Top Travellers

### Problem

#### Table: Users

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| name          | varchar |

- `id` is the column with unique values for this table.
- `name` is the name of the user.

#### Table: Rides

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| user_id       | int     |
| distance      | int     |

- `id` is the column with unique values for this table.
- `user_id` is the ID of the user who traveled the given `distance`.

### Task

Report the total distance traveled by each user. The result table should be:
- Ordered by `travelled_distance` in descending order.
- If two users have the same `travelled_distance`, order them by `name` in ascending order.

### Example

#### Input

**Users** table:

| id   | name      |
|------|-----------|
| 1    | Alice     |
| 2    | Bob       |
| 3    | Alex      |
| 4    | Donald    |
| 7    | Lee       |
| 13   | Jonathan  |
| 19   | Elvis     |

**Rides** table:

| id   | user_id  | distance |
|------|----------|----------|
| 1    | 1        | 120      |
| 2    | 2        | 317      |
| 3    | 3        | 222      |
| 4    | 7        | 100      |
| 5    | 13       | 312      |
| 6    | 19       | 50       |
| 7    | 7        | 120      |
| 8    | 19       | 400      |
| 9    | 7        | 230      |

#### Output

| name     | travelled_distance |
|----------|--------------------|
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |

### Thought Process

1. **Understand Relationships:**
   - The `Users` table contains all users, while the `Rides` table contains information about the distances traveled by users.
   - Some users may not have any rides.

2. **Combine Tables:**
   - Use a `LEFT JOIN` to combine `Users` with `Rides`, ensuring all users appear in the result even if they have no rides.

3. **Calculate Total Distance:**
   - Use `SUM(distance)` to calculate the total distance traveled by each user.
   - Handle cases where a user has no rides by using `0` as the default.

4. **Order Results:**
   - Order by `travelled_distance` in descending order.
   - If distances are the same, order by `name` in ascending order.

---

### Solution

```sql
WITH cte AS (
    SELECT 
        u.name,
        IFNULL(SUM(r.distance), 0) AS travelled_distance
    FROM Users u
    LEFT JOIN Rides r
    ON u.id = r.user_id
    GROUP BY u.name
)
SELECT 
    name, 
    travelled_distance
FROM cte
ORDER BY travelled_distance DESC, name ASC;
```

---

### Solution Explanation

1. **Common Table Expression (CTE):**
   - Joins `Users` with `Rides` using `LEFT JOIN` to ensure all users are included.
   - Calculates the total distance traveled for each user using `SUM(distance)`. If no rides exist for a user, `IFNULL` ensures the total is `0`.

2. **Main Query:**
   - Selects `name` and `travelled_distance` from the CTE.
   - Orders the result by `travelled_distance` in descending order. For ties, sorts by `name` in ascending order.

---

### Key Takeaways

- **LEFT JOIN:** Ensures all records from the `Users` table are included, even if there is no matching record in `Rides`.
- **Aggregate Functions:** Use `SUM` to calculate totals and handle nulls with `IFNULL`.
- **Sorting:** Combine multiple sorting criteria to meet specific requirements.

---

### Related Topics

- SQL Joins
- Aggregate Functions
- Sorting Results in SQL
