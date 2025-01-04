# Employees Whose Manager Left the Company
### Problem Description

We are tasked with finding the `employee_id` of employees who:
1. Have a salary less than $30,000.
2. Report to a manager who has left the company. When a manager leaves, their `employee_id` is removed from the `Employees` table, but the reports still reference their `manager_id`.

The result should be sorted in ascending order of `employee_id`.

---

### Query
```sql
SELECT employee_id 
FROM Employees 
WHERE salary < 30000
  AND manager_id NOT IN (SELECT employee_id FROM Employees)
ORDER BY employee_id;
```

---

### Explanation
1. **Filter salary**:
   - Use `WHERE salary < 30000` to select employees whose salary is strictly less than $30,000.
2. **Check for missing managers**:
   - Use `manager_id NOT IN (SELECT employee_id FROM Employees)` to identify employees whose `manager_id` does not exist in the `employee_id` column, indicating that their manager has left the company.
3. **Sorting**:
   - Use `ORDER BY employee_id` to ensure the results are sorted in ascending order of `employee_id`.

---

### Example Execution
#### Input:
**Employees table:**
| employee_id | name      | manager_id | salary |
|-------------|-----------|------------|--------|
| 3           | Mila      | 9          | 60301  |
| 12          | Antonella | null       | 31000  |
| 13          | Emery     | null       | 67084  |
| 1           | Kalel     | 11         | 21241  |
| 9           | Mikaela   | null       | 50937  |
| 11          | Joziah    | 6          | 28485  |

#### Execution Steps:
1. Filter employees with `salary < 30000`:
   - Results: `employee_id` 1 (Kalel) and 11 (Joziah).
2. Check if their `manager_id` exists in the `employee_id` column:
   - Kalel (`manager_id = 11`): Joziah exists in the table, so exclude Kalel.
   - Joziah (`manager_id = 6`): Manager 6 does not exist in the table, so include Joziah.
3. Sort the results by `employee_id`.

#### Output:
| employee_id |
|-------------|
| 11          |

---

### Key Takeaways
1. `NOT IN` is useful for identifying rows where a reference column does not exist in a related table or column.
2. Filtering conditions can be layered with logical `AND` to ensure all criteria are met.
3. Sorting the final output ensures readability and meets the problem's requirements.


