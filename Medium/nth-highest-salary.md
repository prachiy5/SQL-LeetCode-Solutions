### Nth Highest Salary

Table: Employee
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
```
id is the primary key (column with unique values) for this table.
Each row of this table contains information about the salary of an employee.
 

Write a solution to find the nth highest salary from the Employee table. If there is no nth highest salary, return null.

The result format is in the following example.

 

### Example 1:

**Input:** 

Employee table:
```
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```
n = 2

**Output:** 
```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```
### Example 2:

**Input:** 

Employee table:
```
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+
```
n = 2

**Output:** 
```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| null                   |
+------------------------+
```
### Thought Process:

1. **Understand the Requirement**: The task is to create a function that returns the nth highest distinct salary. If the requested rank doesn't exist in the dataset (e.g., requesting the 2nd highest salary in a table with only one salary), the result should return `null`.

2. **Key Points**:
   - Use SQL's `LIMIT` and `OFFSET` for rank calculation.
   - Use `DISTINCT` to ensure uniqueness of salaries.
   - Handle the case where `n` exceeds the number of distinct salaries, returning `null`.

3. **Approach**:
   - Create a function `getNthHighestSalary` that accepts `N` as a parameter.
   - Query the `Employee` table, order salaries in descending order, and use `LIMIT` and `OFFSET` to fetch the nth highest value.
   - Ensure proper handling of edge cases like when `N` exceeds the number of unique salaries.

4. **Edge Cases**:
   - Table is empty.
   - `N` exceeds the count of distinct salaries.
   - Duplicate salaries in the table.

### Query and Function:

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    SET N = N - 1; -- Adjust N to match SQL's zero-based indexing for OFFSET

    RETURN (
        SELECT DISTINCT(salary) 
        FROM Employee 
        ORDER BY salary DESC
        LIMIT 1 OFFSET N
    );
END;
```

### Explanation of the Function:

1. **Input Parameter**: The function takes an integer `N` representing the rank of the salary we want to find.
2. **Adjust for Offset**: Subtract 1 from `N` to convert it to zero-based indexing, as SQL's `OFFSET` starts at 0.
3. **Select Distinct Salaries**: Use `DISTINCT` to ensure duplicate salaries are not considered multiple times.
4. **Order and Limit**:
   - `ORDER BY salary DESC` ensures salaries are sorted from highest to lowest.
   - `LIMIT 1 OFFSET N` fetches the nth highest salary.
5. **Return Result**: The query result is returned directly. If `N` exceeds the number of distinct salaries, `null` is returned automatically.

### Example Execution:

#### Example 1:
- Input: `n = 2`
- Distinct salaries: `[300, 200, 100]`
- Second highest salary: `200`
- Output: `200`

#### Example 2:
- Input: `n = 2`
- Distinct salaries: `[100]`
- Second highest salary does not exist.
- Output: `null`
