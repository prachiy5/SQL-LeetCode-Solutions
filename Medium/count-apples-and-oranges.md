# Count Apples and Oranges
### Problem Statement

You are given two tables: **Boxes** and **Chests**. The **Boxes** table contains information about individual boxes, including the number of apples and oranges they have. Some boxes may contain a chest, which can also have its own apples and oranges. The **Chests** table provides information about the contents of these chests.

Write a SQL query to calculate the total number of apples and oranges across all boxes. If a box contains a chest, the apples and oranges in the chest should also be included in the box's totals.

### Input Tables

#### Boxes Table:

| box_id | chest_id | apple_count | orange_count |
|--------|----------|-------------|--------------|
| 2      | null     | 6           | 15           |
| 18     | 14       | 4           | 15           |
| 19     | 3        | 8           | 4            |
| 12     | 2        | 19          | 20           |
| 20     | 6        | 12          | 9            |
| 8      | 6        | 9           | 9            |
| 3      | 14       | 16          | 7            |

#### Chests Table:

| chest_id | apple_count | orange_count |
|----------|-------------|--------------|
| 6        | 5           | 6            |
| 14       | 20          | 10           |
| 2        | 8           | 8            |
| 3        | 19          | 4            |
| 16       | 19          | 19           |

### Output

The result should be a single row with the total number of apples and oranges:

| apple_count | orange_count |
|-------------|--------------|
| 151         | 123          |

### Explanation

- Box `2` has no chest and contains 6 apples and 15 oranges.
- Box `18` contains chest `14`, adding 20 apples and 10 oranges to its own 4 apples and 15 oranges.
- Box `19` contains chest `3`, adding 19 apples and 4 oranges to its own 8 apples and 4 oranges.
- Box `12` contains chest `2`, adding 8 apples and 8 oranges to its own 19 apples and 20 oranges.
- Box `20` contains chest `6`, adding 5 apples and 6 oranges to its own 12 apples and 9 oranges.
- Box `8` also contains chest `6`, adding 5 apples and 6 oranges to its own 9 apples and 9 oranges.
- Box `3` contains chest `14`, adding 20 apples and 10 oranges to its own 16 apples and 7 oranges.

### Thought Process
1. **Understand the problem:** Each box can optionally contain a chest. If it does, the contents of the chest should be added to the box's contents.
2. **Join the tables:** Perform a `LEFT JOIN` between the **Boxes** and **Chests** tables using the `chest_id` column.
3. **Handle null values:** Use `COALESCE` to treat null values (i.e., boxes without chests) as zeros for apples and oranges.
4. **Aggregate totals:** Use the `SUM` function to calculate the total number of apples and oranges across all boxes.
5. **Return results:** Ensure the output contains the total counts of apples and oranges.

### Query
```sql
SELECT 
    SUM(b.apple_count + COALESCE(c.apple_count, 0)) AS apple_count,
    SUM(b.orange_count + COALESCE(c.orange_count, 0)) AS orange_count
FROM 
    Boxes b
LEFT JOIN 
    Chests c
ON 
    b.chest_id = c.chest_id;
```

### Explanation of the Query
1. **FROM and JOIN:** The `Boxes` table is the main table, and a `LEFT JOIN` ensures all rows from `Boxes` are included, even if a `chest_id` is null.
2. **COALESCE:** Handles cases where a box does not have a chest by treating null values as zeros.
3. **SUM:** Aggregates the total number of apples and oranges from both the boxes and the chests.

### Result
| apple_count | orange_count |
|-------------|--------------|
| 151         | 123          |
