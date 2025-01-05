# Find Median Given Frequency of Numbers

You are given a table:

### Numbers Table
| Column Name | Type |
|-------------|------|
| num         | int  |
| frequency   | int  |

- `num` is the primary key (column with unique values) for this table.
- Each row in this table shows the frequency of a number in the database.

### Task
The **median** is the value separating the higher half from the lower half of a data sample. Write a solution to report the median of all the numbers in the database **after decompressing the Numbers table**. Round the median to one decimal point.

### Input Example
#### Numbers Table:
| num | frequency |
|-----|-----------|
| 0   | 7         |
| 1   | 1         |
| 2   | 3         |
| 3   | 1         |

### Output Example
| median |
|--------|
| 0.0    |

### Explanation

1. **Decompression**:
   - Expand the `Numbers` table based on the `frequency` column.
   - The decompressed sequence is: `[0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3]`.

2. **Find the Median**:
   - Sort the decompressed sequence: `[0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3]`.
   - The total number of elements is 12 (even).
   - The median is calculated as the average of the two middle numbers:
     - Middle numbers: `0` (6th element) and `0` (7th element).
     - Median = `(0 + 0) / 2 = 0.0`.

### Thought Process

1. **Cumulative Frequency**:
   - Use a cumulative sum of the `frequency` column to determine the position of each number in the decompressed sequence.

2. **Total Frequency and Median Position**:
   - Calculate the total frequency of all numbers.
   - Identify the median position(s):
     - If the total frequency is odd: The median is the number at the `(total frequency / 2 + 1)` position.
     - If the total frequency is even: The median is the average of the numbers at the `(total frequency / 2)` and `(total frequency / 2 + 1)` positions.

3. **Filter Median Numbers**:
   - Identify rows where the cumulative frequency includes the median position(s).

4. **Calculate the Median**:
   - Use the `AVG` function to calculate the median for the identified rows.

### Query
```sql
WITH cte AS (
    SELECT 
        num,
        frequency,
        SUM(frequency) OVER (ORDER BY num) AS cum_frequency,
        SUM(frequency) OVER () AS total_frequency,
        SUM(frequency) OVER () / 2 AS median_position
    FROM 
        Numbers
)

SELECT 
    AVG(num) AS median
FROM 
    cte
WHERE 
    median_position BETWEEN cum_frequency - frequency AND cum_frequency;
