### Question:

Table: Heights

| Column Name | Type |
|-------------|------|
| id          | int  |
| height      | int  |

- `id` is the primary key (column with unique values) for this table, and it is guaranteed to be in sequential order.
- Each row of this table contains an `id` and `height`.

Write a solution to calculate the amount of rainwater that can be trapped between the bars in the landscape, considering that each bar has a width of 1 unit.

Return the result table in any order.

---

### Example:

#### Input:

Heights table:

| id  | height |
|-----|--------|
| 1   | 0      |
| 2   | 1      |
| 3   | 0      |
| 4   | 2      |
| 5   | 1      |
| 6   | 0      |
| 7   | 1      |
| 8   | 3      |
| 9   | 2      |
| 10  | 1      |
| 11  | 2      |
| 12  | 1      |

#### Output:

| total_trapped_water |
|---------------------|
| 6                   |

#### Explanation:

The elevation map is depicted graphically with the x-axis representing the `id` and the y-axis representing the `heights` [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rainwater are trapped within the blue section of the map.

---

### Thought Process:

1. **Understand the Problem**:
   - Water can only be trapped if there are higher bars on both sides of a given bar.
   - The trapped water at a specific position is determined by the smaller of the maximum heights on the left and right minus the height of the current bar.

2. **Calculate Maximum Heights**:
   - Use window functions to compute the `left_max` for each position (the maximum height to the left, including the current bar).
   - Similarly, calculate the `right_max` for each position (the maximum height to the right, including the current bar).

3. **Calculate Trapped Water**:
   - For each bar, compute the trapped water using the formula: `trapped_water = max(0, min(left_max, right_max) - height)`.

4. **Sum the Trapped Water**:
   - Aggregate the trapped water values for all positions to get the total amount of water trapped.

5. **Output the Result**:
   - Return the total trapped water as the result.

---

### Query:

```sql
WITH left_max AS (
    SELECT 
        id,
        height,
        MAX(height) OVER (ORDER BY id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS left_max
    FROM 
        heights
),

right_max AS (
    SELECT 
        id,
        height,
        left_max,
        MAX(height) OVER (ORDER BY id DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS right_max
    FROM 
        left_max
    ORDER BY 
        id
)

SELECT 
    SUM(GREATEST(LEAST(left_max, right_max) - height, 0)) AS total_trapped_water
FROM 
    right_max;
