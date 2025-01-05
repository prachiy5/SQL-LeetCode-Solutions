# Students Report By Geography

You are given a table:

### Student Table
| Column Name | Type    |
|-------------|---------|
| name        | varchar |
| continent   | varchar |

- This table may contain duplicate rows.
- Each row of this table indicates the name of a student and the continent they come from.

### Task
A school has students from **Asia**, **Europe**, and **America**. Write a solution to pivot the `continent` column in the `Student` table so that each name is sorted alphabetically and displayed underneath its corresponding continent.

- The output headers should be **America**, **Asia**, and **Europe**, respectively.

### Constraints
- The test cases are generated so that the student count from **America** is not less than that of either **Asia** or **Europe**.

### Result Requirements
- The result table should include:
  - Each student's name placed under their respective continent column.
  - If a continent has fewer students than others, use `null` to fill the empty spots.
- Order student names alphabetically within each continent.

### Input Example
#### Student Table:
| name   | continent |
|--------|-----------|
| Jane   | America   |
| Pascal | Europe    |
| Xi     | Asia      |
| Jack   | America   |

### Output Example
| America | Asia | Europe |
|---------|------|--------|
| Jack    | Xi   | Pascal |
| Jane    | null | null   |

### Follow-Up
If it is unknown which continent has the most students, could you write a solution to generate the student report?

---

# Thought Process

1. **Partition Students by Continent**:
   - Use the `ROW_NUMBER()` function to assign a unique rank to each student within their respective continent, ordering their names alphabetically.

2. **Pivot Rows into Columns**:
   - Use a `CASE` statement to assign student names to their corresponding continent columns.
   - Group by the rank (`row_id`) generated earlier to align names from different continents into rows.

3. **Handle Uneven Data**:
   - Ensure that rows are filled with `null` values where a continent has fewer students than the maximum number of rows.

4. **Order Result**:
   - The result table does not require any specific row order, as per the problem statement.

---

# Query
```sql
SELECT
    MAX(CASE WHEN continent = 'America' THEN name END) AS America,
    MAX(CASE WHEN continent = 'Asia' THEN name END) AS Asia,
    MAX(CASE WHEN continent = 'Europe' THEN name END) AS Europe
FROM 
    (SELECT *, ROW_NUMBER() OVER (PARTITION BY continent ORDER BY name) AS row_id FROM Student) AS t
GROUP BY 
    row_id;
