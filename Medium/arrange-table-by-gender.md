### Arrange Table by Gender

Table: Genders

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| gender      | varchar |

- `user_id` is the primary key with unique values.
- `gender` is an ENUM with possible values: 'female', 'male', or 'other'.
- Each row in this table contains the ID of a user and their gender.
- The table has an equal number of users for each gender.

#### Task
Rearrange the table such that rows alternate between 'female', 'other', and 'male' in this order. The IDs within each gender group must be sorted in ascending order.

#### Expected Output
Return a result table where the rows alternate between genders as described above. The output format is as follows:

| user_id | gender |
|---------|--------|
| 3       | female |
| 1       | other  |
| 4       | male   |
| 7       | female |
| 2       | other  |
| 5       | male   |
| 9       | female |
| 6       | other  |
| 8       | male   |

### Thought Process

1. **Group and Sort by Gender:** Use `ROW_NUMBER()` to assign a sequential number to each user within each gender group, sorted by `user_id`.

2. **Assign Priority Order:** Assign a priority value to each gender:
   - 'female' → Priority 1
   - 'other' → Priority 2
   - 'male' → Priority 3

3. **Combine Rows in Alternating Order:** Using the row numbers and priority values, sort the rows to alternate between genders ('female', 'other', 'male').

4. **Return Result:** Select the `user_id` and `gender` columns in the desired order.

### SQL Query

```sql
WITH cte AS (
    SELECT 
        user_id,
        gender,
        ROW_NUMBER() OVER (PARTITION BY gender ORDER BY user_id) AS rn,
        CASE 
            WHEN gender = 'female' THEN 1
            WHEN gender = 'male' THEN 3
            ELSE 2
        END AS rn2
    FROM genders
)

SELECT 
    user_id,
    gender
FROM 
    cte
ORDER BY 
    rn, rn2;
```
