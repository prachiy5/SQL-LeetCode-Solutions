### Total Traveled Distance

**Table: Users**

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| name        | varchar |

- `user_id` is the column with unique values for this table.
- Each row of this table contains user id and name.

**Table: Rides**

| Column Name  | Type |
|--------------|------|
| ride_id      | int  |
| user_id      | int  |
| distance     | int  |

- `ride_id` is the column of unique values for this table.
- Each row of this table contains ride id, user id, and traveled distance.

Write a solution to calculate the total distance traveled by each user. If a user hasn’t completed any rides, their total traveled distance should be considered as `0`. Return the result table with columns `user_id`, `name`, and `traveled distance`. The result should be ordered by `user_id` in ascending order.

#### Example:

**Input:**

**Users table:**

| user_id | name    |
|---------|---------|
| 17      | Addison |
| 14      | Ethan   |
| 4       | Michael |
| 2       | Avery   |
| 10      | Eleanor |

**Rides table:**

| ride_id | user_id | distance |
|---------|---------|----------|
| 72      | 17      | 160      |
| 42      | 14      | 161      |
| 45      | 4       | 59       |
| 32      | 2       | 197      |
| 15      | 4       | 357      |
| 56      | 2       | 196      |
| 10      | 14      | 25       |

**Output:**

| user_id | name    | traveled distance |
|---------|---------|-------------------|
| 2       | Avery   | 393               |
| 4       | Michael | 416               |
| 10      | Eleanor | 0                 |
| 14      | Ethan   | 186               |
| 17      | Addison | 160               |

#### Explanation:
- User `2` completed two journeys of `197` and `196`, resulting in a combined travel distance of `393`.
- User `4` completed two journeys of `59` and `357`, resulting in a combined travel distance of `416`.
- User `14` completed two journeys of `161` and `25`, resulting in a combined travel distance of `186`.
- User `17` completed only one journey of `160`.
- User `10` did not complete any journeys, thus the total travel distance remains at `0`.

---

### Thought Process:

1. **Understand the Requirements:** We need to compute the total distance traveled for each user by summing up the `distance` column from the `Rides` table for each `user_id`. Users with no rides should have a total distance of `0`.

2. **Plan:**
   - Use a `LEFT JOIN` to combine the `Users` table with the `Rides` table. This ensures that all users are included, even if they don’t have any corresponding records in the `Rides` table.
   - Use `SUM(distance)` to calculate the total distance for each user.
   - Use `IFNULL` to handle cases where there are no rides for a user, setting their total distance to `0`.
   - Group the results by `user_id` and `name`.
   - Order the results by `user_id` in ascending order.

3. **Query Structure:**
   - Create a Common Table Expression (CTE) to join `Users` and `Rides`.
   - Aggregate the results with `SUM` and handle `NULL` values using `IFNULL`.

---

### Query:

```sql
WITH cte AS (
    SELECT 
        u.user_id, 
        u.name,
        r.distance
    FROM 
        Users u
    LEFT JOIN 
        Rides r
    ON 
        u.user_id = r.user_id
)

SELECT 
    user_id,
    name,
    IFNULL(SUM(distance), 0) AS `traveled distance`
FROM 
    cte
GROUP BY 
    user_id, name
ORDER BY 
    user_id;
```

---

### Explanation of the Query:

1. **CTE (Common Table Expression):**
   - Joins the `Users` table with the `Rides` table on `user_id`.
   - Includes all rows from `Users` even if there are no matching rows in `Rides` due to the `LEFT JOIN`.

2. **Aggregation:**
   - Groups the data by `user_id` and `name`.
   - Calculates the sum of `distance` for each user using `SUM(distance)`.
   - Handles `NULL` values using `IFNULL`, replacing them with `0`.

3. **Ordering:**
   - Sorts the result by `user_id` in ascending order.

---

### Why This Works:
- Ensures that every user is included in the result, even if they haven’t taken any rides.
- Correctly calculates the total traveled distance for users with rides.
- Outputs the result in the required order.


