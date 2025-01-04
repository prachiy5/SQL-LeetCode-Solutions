# Convert Date Format
### Problem Description
We want to transform the date values in the `Days` table into a specific string format: "day_name, month_name day, year". For example, the date `2022-04-12` should be formatted as "Tuesday, April 12, 2022".

### Input Table Structure
The input table `Days` has the following structure:

```text
+-------------+------+
| Column Name | Type |
+-------------+------+
| day         | date |
+-------------+------+
```
- `day` is a unique date value.

### Output Table
The output should contain a single column `day` with the dates formatted as described above.

### Solution
We use the `DATE_FORMAT` function in MySQL, which allows us to format date values into a specific string format. 

**Query:**
```sql
SELECT DATE_FORMAT(day, '%W, %M %e, %Y') AS day
FROM Days;
```

### Thought Process
1. **Understand the Problem:** We need to format dates from the `Days` table into a human-readable string format with:
   - Full day name (e.g., "Tuesday")
   - Full month name (e.g., "April")
   - Day of the month without leading zeros (e.g., "12")
   - Full year (e.g., "2022")
2. **Identify the Formatting Options:** Use the `DATE_FORMAT` function in MySQL, with the following format specifiers:
   - `%W` for the full weekday name.
   - `%M` for the full month name.
   - `%e` for the day of the month without leading zeros.
   - `%Y` for the 4-digit year.
3. **Query Design:** Apply the `DATE_FORMAT` function to the `day` column and alias the result as `day` for the output.

### Explanation of the Query
- `DATE_FORMAT(day, '%W, %M %e, %Y')`: Formats the date into the required string structure.
- `FROM Days`: Retrieves all rows from the `Days` table.

### Example Input and Output
#### Input:
```text
+------------+
| day        |
+------------+
| 2022-04-12 |
| 2021-08-09 |
| 2020-06-26 |
+------------+
```

#### Output:
```text
+-------------------------+
| day                     |
+-------------------------+
| Tuesday, April 12, 2022 |
| Monday, August 9, 2021  |
| Friday, June 26, 2020   |
+-------------------------+
```

### Key Takeaways
1. Use `DATE_FORMAT` for flexible date formatting in MySQL.
2. Leverage appropriate format specifiers for day, month, and year.
3. Always verify the case-sensitivity of the output to match the requirements.
