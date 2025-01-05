# Human Traffic of Stadium

You are given a table:

### Stadium Table
| Column Name | Type |
|-------------|------|
| id          | int  |
| visit_date  | date |
| people      | int  |

- `visit_date` is the column with unique values for this table.
- Each row of this table contains the visit date and visit id to the stadium, along with the number of people during the visit.
- As the `id` increases, the `visit_date` increases as well.

### Task
Write a solution to display the records with **three or more rows with consecutive `id`s**, and where the number of people is greater than or equal to 100 for each row.

### Result Requirements
- Return the result table ordered by `visit_date` in ascending order.

### Input Example
#### Stadium Table:
| id | visit_date | people |
|----|------------|--------|
| 1  | 2017-01-01 | 10     |
| 2  | 2017-01-02 | 109    |
| 3  | 2017-01-03 | 150    |
| 4  | 2017-01-04 | 99     |
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 1455   |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-09 | 188    |

### Output Example
| id | visit_date | people |
|----|------------|--------|
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 1455   |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-09 | 188    |

### Explanation

1. **Rows with ids 5, 6, 7, and 8**:
   - These rows have consecutive `id`s and each row has `people >= 100`.
   - Row 8 is included even though its `visit_date` is not the next day after row 7, because the condition is based on consecutive `id`s.

2. **Rows with ids 2 and 3**:
   - These rows are not included because we need at least **three consecutive ids**.

### Thought Process

1. **Filter Rows with `people >= 100`**:
   - Only include rows where the `people` column is greater than or equal to 100.

2. **Identify Consecutive Groups**:
   - Use the `ROW_NUMBER()` function to assign a sequential rank to rows based on the `id`.
   - Subtract the `id` from the `ROW_NUMBER()` to create a `group` identifier for consecutive rows.

3. **Filter Groups with at Least Three Rows**:
   - Count the number of rows in each group.
   - Only include groups that have three or more rows.

4. **Retrieve the Relevant Rows**:
   - Select the rows from the filtered groups.

5. **Ordering**:
   - Order the results by `visit_date` in ascending order.

### Query
```sql
WITH cte_rownum AS (
    SELECT 
        id,
        visit_date,
        people,
        (id - ROW_NUMBER() OVER (ORDER BY id)) AS rw
    FROM 
        stadium
    WHERE 
        people >= 100
),

CTE_withGroup AS (
    SELECT 
        COUNT(id) AS cnt, 
        rw
    FROM 
        cte_rownum
    GROUP BY 
        rw
    HAVING 
        COUNT(id) >= 3
)

SELECT 
    id,
    visit_date,
    people
FROM 
    cte_rownum
WHERE 
    rw IN (SELECT rw FROM CTE_withGroup)
ORDER BY 
    visit_date ASC;
