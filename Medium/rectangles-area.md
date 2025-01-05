### Rectangles Area
We are given a table:

#### Table: `Points`

| Column Name | Type |
|-------------|------|
| id          | int  |
| x_value     | int  |
| y_value     | int  |

- `id` is the primary key.
- Each row represents a 2D point with coordinates `(x_value, y_value)`.

### Task
Write a SQL query to find all possible **axis-aligned rectangles** with a non-zero area that can be formed by any two points in the `Points` table.

#### Constraints:
1. Each row in the result must contain:
   - `p1`: The `id` of one corner point.
   - `p2`: The `id` of the opposite corner point.
   - `area`: The calculated area of the rectangle.
2. The result should exclude cases where the area of the rectangle is zero.
3. Order the result by:
   - `area` in descending order.
   - If there is a tie, order by `p1` in ascending order.
   - If there is still a tie, order by `p2` in ascending order.

---

### Example
#### Input:
**Points table:**

| id | x_value | y_value |
|----|---------|---------|
| 1  | 2       | 7       |
| 2  | 4       | 8       |
| 3  | 2       | 10      |

#### Output:

| p1 | p2 | area |
|----|----|------|
| 2  | 3  | 4    |
| 1  | 2  | 2    |

#### Explanation:
1. The rectangle formed by points `p1 = 2` and `p2 = 3` has:
   - Width = \(|4 - 2| = 2\)
   - Height = \(|8 - 10| = 2\)
   - Area = \(2 \times 2 = 4\)

2. The rectangle formed by points `p1 = 1` and `p2 = 2` has:
   - Width = \(|2 - 4| = 2\)
   - Height = \(|7 - 8| = 1\)
   - Area = \(2 \times 1 = 2\)

3. The rectangle formed by points `p1 = 1` and `p2 = 3` has zero area, so it is excluded.

---

### Thought Process
1. **Pair Points:**
   - Use a self-join to pair all possible points `(p1, p2)` where `p1.id < p2.id` to avoid duplicates and self-pairing.

2. **Filter Non-Zero Area:**
   - Ensure the points form a valid rectangle by checking that their `x_value` and `y_value` are different.

3. **Calculate Area:**
   - Use the formula \(\text{area} = |x_1 - x_2| \times |y_1 - y_2|\).

4. **Sort Results:**
   - Order the results by `area` (descending), then `p1` (ascending), and finally `p2` (ascending).

---

### SQL Query
```sql
SELECT 
    p1.id AS p1,
    p2.id AS p2,
    ABS(p1.x_value - p2.x_value) * ABS(p1.y_value - p2.y_value) AS area
FROM 
    points p1
JOIN 
    points p2
ON 
    p1.id < p2.id
WHERE 
    p1.x_value != p2.x_value AND p1.y_value != p2.y_value
ORDER BY 
    area DESC, p1, p2;
```

---

### Key Points
1. **Self-Join:**
   - The `JOIN` pairs all possible combinations of points without duplication by ensuring `p1.id < p2.id`.

2. **Filter Non-Zero Area:**
   - Rectangles require that the x-coordinates and y-coordinates differ for the two points.

3. **Sorting:**
   - Ensure the results are ordered correctly by area, then by point IDs.

### Related Topics
- Self-Joins in SQL
- Calculating Areas in 2D Space
- Ordering Results in SQL
