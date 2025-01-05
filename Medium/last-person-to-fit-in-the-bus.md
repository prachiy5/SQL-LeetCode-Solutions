### Last Person to Fit in the Bus
We are given a table:

#### Table: `Queue`

| Column Name | Type    |
|-------------|---------|
| person_id   | int     |
| person_name | varchar |
| weight      | int     |
| turn        | int     |

- `person_id` contains unique values.
- The `turn` column determines the order of boarding the bus, with `turn=1` as the first person to board and `turn=n` as the last person to board.
- `weight` is the weight of the person in kilograms.
- The bus has a weight limit of **1000 kilograms**, so some people may not be able to board.

### Task
Write a SQL query to find the `person_name` of the last person who can board the bus without exceeding the weight limit. The first person can always board as their weight does not exceed the limit.

### Example
#### Input:
**Queue table:**

| person_id | person_name | weight | turn |
|-----------|-------------|--------|------|
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |

#### Output:

| person_name |
|-------------|
| John Cena   |

#### Explanation:
1. Sort the table by `turn`:

| Turn | ID | Name      | Weight | Total Weight |
|------|----|-----------|--------|--------------|
| 1    | 5  | Alice     | 250    | 250          |
| 2    | 3  | Alex      | 350    | 600          |
| 3    | 6  | John Cena | 400    | 1000         | (last person to board)
| 4    | 2  | Marie     | 200    | 1200         | (cannot board)
| 5    | 4  | Bob       | 175    | ---          |
| 6    | 1  | Winston   | 500    | ---          |

2. The total weight reaches `1000` after John Cena boards. Any additional person would exceed the weight limit.

---

### Thought Process
1. **Sort by Turn**: People board the bus in the order of `turn`, so ensure the calculations follow this order.
2. **Cumulative Weight Calculation**: Use a window function to calculate the cumulative weight (`SUM(weight)`) for all people boarding the bus up to each turn.
3. **Filter by Weight Limit**: Identify rows where the cumulative weight is less than or equal to `1000`.
4. **Find the Last Person**: Select the person with the maximum cumulative weight that is still within the weight limit.

---

### SQL Query
```sql
WITH cte AS (
    SELECT 
        person_name, 
        SUM(weight) OVER (ORDER BY turn) AS total_weight
    FROM Queue
)

SELECT 
    person_name
FROM cte
WHERE total_weight <= 1000
ORDER BY total_weight DESC
LIMIT 1;
```

---

### Key Takeaways
1. **Window Functions**: Use `SUM()` with `OVER (ORDER BY column)` to calculate running totals.
2. **Filtering**: Use a `WHERE` clause to keep rows within the weight limit.
3. **Ordering and Limiting**: Use `ORDER BY` and `LIMIT` to select the person with the highest valid cumulative weight.
4. **Guaranteed Valid Input**: The problem guarantees that the first person can always board, simplifying edge case handling.

### Related Topics
- Window Functions
- Filtering with `WHERE`
- Cumulative Totals
- Query Optimization
