# Shortest Distance in a Line

### Problem

#### Table: Point

| Column Name | Type |
|-------------|------|
| x           | int  |

- `x` is the primary key.
- Each row represents the position of a point on the X-axis.

#### Task
Find the shortest distance between any two points from the `Point` table.

#### Formula for Distance
The distance between two points on the X-axis, `x1` and `x2`, is given by:

```
Distance = |x1 - x2|
```

#### Return Format
Return a table with a single column named `shortest` that contains the shortest distance.

---

### Example

**Input:**

`Point` table:

| x  |
|----|
| -1 |
|  0 |
|  2 |

**Output:**

| shortest |
|----------|
| 1        |

**Explanation:**
- The distances between points are:
  - Between `-1` and `0`: `|-1 - 0| = 1`
  - Between `-1` and `2`: `|-1 - 2| = 3`
  - Between `0` and `2`: `|0 - 2| = 2`
- The shortest distance is `1`.

---

### Thought Process

1. **Pair All Points:**
   - Use a `JOIN` operation to pair every point with every other point in the table.

2. **Calculate Distance:**
   - Use the `ABS()` function to compute the absolute difference between the two points.

3. **Find the Shortest Distance:**
   - Use the `MIN()` function to identify the smallest distance.

4. **Optimization for Ordered Data:**
   - If the table is sorted, you only need to compare consecutive points, reducing the number of comparisons significantly.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT MIN(ABS(p1.x - p2.x)) AS shortest
FROM Point p1
JOIN Point p2 ON p1.x < p2.x;
```

---

### Solution Explanation

1. **JOIN Operation:**
   - Pair each point `p1` with every other point `p2` where `p1.x < p2.x`.
   - This ensures each pair is considered only once.

2. **Distance Calculation:**
   - Use `ABS(p1.x - p2.x)` to calculate the absolute difference between the two points.

3. **Shortest Distance:**
   - Use `MIN()` to find the smallest distance among all pairs.

---

### Optimization for Ordered Data
If the table is already ordered in ascending order, the shortest distance can only exist between consecutive points. In this case:

1. Use a `LAG()` or `LEAD()` window function to compare each point with its next neighbor.
2. Calculate the difference and find the minimum.

```sql
# Optimized Solution for Ordered Data
SELECT MIN(x - prev_x) AS shortest
FROM (
  SELECT x, LAG(x) OVER (ORDER BY x) AS prev_x
  FROM Point
) subquery
WHERE prev_x IS NOT NULL;
```

---

### Key Takeaways

- **Pairwise Comparisons:** Use `JOIN` to pair all points and calculate distances.
- **Optimization for Ordered Data:** Compare only consecutive points using window functions.
- **MIN Function:** Ideal for finding the smallest value in a set.

---

### Related Topics
- SQL JOINs
- Window Functions (LAG/LEAD)
- Aggregate Functions (MIN, MAX)
