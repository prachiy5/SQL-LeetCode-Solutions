# Employees With Missing Information

### Problem Description
We need to find the IDs of employees with missing information. An employee's information is considered missing if either:
1. Their name is missing in the `Employees` table, or
2. Their salary is missing in the `Salaries` table.

We will return the IDs of such employees, sorted in ascending order.

---

### Input Tables
**Employees Table:**
| employee_id | name     |
|-------------|----------|
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |

**Salaries Table:**
| employee_id | salary   |
|-------------|----------|
| 5           | 76071    |
| 1           | 22517    |
| 4           | 63539    |

---

### Expected Output
| employee_id |
|-------------|
| 1           |
| 2           |

Explanation:
- Employee `1` is missing from the `Employees` table.
- Employee `2` is missing from the `Salaries` table.

---

### Thought Process
1. **Identify Missing Names:** Use the `Salaries` table to find `employee_id` values not present in the `Employees` table.
2. **Identify Missing Salaries:** Use the `Employees` table to find `employee_id` values not present in the `Salaries` table.
3. **Combine Results:** Use a `UNION` operation to merge the results from steps 1 and 2.
4. **Order Results:** Sort the output by `employee_id` in ascending order.

---

### SQL Query
```sql
SELECT employee_id
FROM Employees
WHERE employee_id NOT IN (SELECT employee_id FROM Salaries)

UNION

SELECT employee_id
FROM Salaries
WHERE employee_id NOT IN (SELECT employee_id FROM Employees)

ORDER BY employee_id;
```

---

### Explanation of the Query
1. **First SELECT Statement:**
   - Fetches `employee_id` values from the `Employees` table where the corresponding `employee_id` does not exist in the `Salaries` table.

2. **Second SELECT Statement:**
   - Fetches `employee_id` values from the `Salaries` table where the corresponding `employee_id` does not exist in the `Employees` table.

3. **UNION:**
   - Combines the results of both SELECT statements, ensuring no duplicate `employee_id` values.

4. **ORDER BY:**
   - Orders the final result set by `employee_id` in ascending order.

---



### Key Takeaways
- Use `NOT IN` to find mismatched records between two tables.
- Use `UNION` to combine results from multiple queries.
- Always use `ORDER BY` for sorted output as required by the problem.
