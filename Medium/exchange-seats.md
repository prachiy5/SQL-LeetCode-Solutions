### Exchange Seats

Table: Seat
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| student     | varchar |
+-------------+---------+
```
id is the primary key (unique value) column for this table.
Each row of this table indicates the name and the ID of a student.
The ID sequence always starts from 1 and increments continuously.

Write a solution to swap the seat id of every two consecutive students. If the number of students is odd, the id of the last student is not swapped.

Return the result table ordered by id in ascending order.

The result format is in the following example.

### Example 1:

Input: 
Seat table:
```
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
```
Output: 
```
+----+---------+
| id | student |
+----+---------+
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |
+----+---------+
```

### Explanation:
Note that if the number of students is odd, there is no need to change the last one's seat.

### Thought Process
1. **Identify Swappable Pairs**:
   - For every pair of consecutive rows (`id % 2 == 1` and `id % 2 == 0`), swap their `id` values.
   - For odd-numbered rows that are not part of a pair (i.e., the last row in an odd-length table), keep their `id` unchanged.

2. **Handling Order**:
   - After swapping the `id` values, reorder the table by the new `id` values to ensure the output aligns with the updated `id` sequence.

3. **Implementation Strategy**:
   - Use a `CASE` statement to determine the new `id` values based on their parity (odd/even).
   - Use the total count of rows to decide the boundary condition for the last odd-numbered row.
   - Return the updated table ordered by the swapped `id` values.

### SQL Query
```sql
WITH total AS (
    SELECT COUNT(*) AS total FROM Seat
)

SELECT 
    CASE 
        WHEN id % 2 = 0 THEN id - 1
        WHEN id % 2 != 0 AND id < (SELECT total FROM total) THEN id + 1
        ELSE id
    END AS id,
    student
FROM 
    Seat, total
ORDER BY 
    id;
```

### Explanation of the Query
1. **CTE for Total Count**:
   - Use a Common Table Expression (CTE) to calculate the total number of rows in the `Seat` table.

2. **Swapping Logic**:
   - If the `id` is even (`id % 2 = 0`), swap it with the previous odd `id` by subtracting 1.
   - If the `id` is odd (`id % 2 != 0`) and not the last row (`id < total`), swap it with the next even `id` by adding 1.
   - If the `id` is odd and it is the last row (`id = total`), keep it unchanged.

3. **Ordering**:
   - Use `ORDER BY id` to return the rows ordered by the new `id` values.

### Example Execution
Given the `Seat` table:

| id  | student  |
|-----|----------|
| 1   | Abbot    |
| 2   | Doris    |
| 3   | Emerson  |
| 4   | Green    |
| 5   | Jeames   |

- **Step 1**: Calculate total rows (`total = 5`).
- **Step 2**: Apply the `CASE` logic:
  - `id = 1`: Swap with `id = 2`.
  - `id = 2`: Swap with `id = 1`.
  - `id = 3`: Swap with `id = 4`.
  - `id = 4`: Swap with `id = 3`.
  - `id = 5`: Keep unchanged.
- **Step 3**: Order by the updated `id`.

Final output:

| id  | student  |
|-----|----------|
| 1   | Doris    |
| 2   | Abbot    |
| 3   | Green    |
| 4   | Emerson  |
| 5   | Jeames   |
