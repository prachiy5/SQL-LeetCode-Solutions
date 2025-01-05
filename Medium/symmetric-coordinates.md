### Symmetric Coordinates
We are given a table:

#### Table: Coordinates
| Column Name | Type |
|-------------|------|
| X           | int  |
| Y           | int  |

- Each row contains integers `X` and `Y`.
- The table may contain duplicate values.

#### Symmetric Coordinates Definition
Two coordinates `(X1, Y1)` and `(X2, Y2)` are said to be **symmetric** if:
1. `X1 == Y2`, and
2. `X2 == Y1`.

#### Task
Write a query to find all unique symmetric coordinates such that:
- `X1 <= Y1`.

Return the result table with the following columns:
- `x`: The first coordinate value.
- `y`: The second coordinate value.

The result should be ordered by `x` and `y` in ascending order.

---

### Example Input/Output

##### Input:
**Coordinates table:**
| X  | Y  |
|----|----|
| 20 | 20 |
| 20 | 20 |
| 20 | 21 |
| 23 | 22 |
| 22 | 23 |
| 21 | 20 |

##### Output:
| x  | y  |
|----|----|
| 20 | 20 |
| 20 | 21 |
| 22 | 23 |

##### Explanation:
1. `(20, 20)` and `(20, 20)` are symmetric because `X1 == Y2` and `X2 == Y1`. Since they are identical, only `(20, 20)` is included in the output.
2. `(20, 21)` and `(21, 20)` are symmetric because `X1 == Y2` and `X2 == Y1`. Only `(20, 21)` is included because `X1 <= Y1`.
3. `(23, 22)` and `(22, 23)` are symmetric because `X1 == Y2` and `X2 == Y1`. Only `(22, 23)` is included because `X1 <= Y1`.
4. The output is sorted by `x` and `y` in ascending order.

---

### Thought Process
1. **Identify Symmetric Coordinates:**
   - Join the table with itself (`Coordinates AS c1` and `Coordinates AS c2`) to find pairs where `c1.x == c2.y` and `c1.y == c2.x`.
2. **Filter by Unique Condition:**
   - To ensure unique coordinates, include only those where `c1.x <= c1.y`.
   - Use `DISTINCT` to remove duplicate rows.
3. **Order Results:**
   - Sort the output by `x` and `y` in ascending order to meet the requirement.

---

### Query
```sql
WITH c AS (
    SELECT *, 
           ROW_NUMBER() OVER (ORDER BY x) AS rn
    FROM coordinates
)

SELECT DISTINCT c1.x, c1.y
FROM c AS c1
JOIN c AS c2
ON c1.x = c2.y 
   AND c1.y = c2.x
   AND c1.x <= c1.y
   AND c1.rn != c2.rn
ORDER BY c1.x, c1.y;
```

---

### Key Takeaways
- **Self-Join:** Joining the table with itself allows comparison of rows to identify symmetric pairs.
- **Condition Filtering:** Filtering on `c1.x <= c1.y` ensures only valid symmetric coordinates are included.
- **Eliminating Duplicates:** Using `DISTINCT` removes redundant rows.
- **Row Numbering:** The `ROW_NUMBER()` function ensures that symmetric pairs are uniquely identified and prevents self-matching.

---

### Related Topics
- SQL Joins
- Window Functions (`ROW_NUMBER`)
- Deduplication with `DISTINCT`
- Sorting and Ordering Results
