
# Calculate Special Bonus
#### Problem Description

We need to calculate the **bonus** for each employee based on the following conditions:

1. If the `employee_id` is an odd number **and** the `name` does not start with the character `'M'`, the bonus is **100% of their salary**.
2. For all other cases, the bonus is **0**.

The result should be ordered by `employee_id`.

---

# Table Details

## Employees

| Column Name  | Type    | Description                                      |
|--------------|---------|--------------------------------------------------|
| employee_id  | int     | Unique ID for each employee (Primary Key).       |
| name         | varchar | The name of the employee.                       |
| salary       | int     | The salary of the employee.                     |

---

# Task

1. Calculate the **bonus** for each employee based on the given conditions.
2. Return the result ordered by `employee_id`.

---

# Input Example

## Employees Table:

| employee_id | name    | salary |
|-------------|---------|--------|
| 2           | Meir    | 3000   |
| 3           | Michael | 3800   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7700   |

---

# Output Example

## Result:

| employee_id | bonus |
|-------------|-------|
| 2           | 0     |
| 3           | 0     |
| 7           | 7400  |
| 8           | 0     |
| 9           | 7700  |

---

# Thought Process

1. **Odd ID Check**: Use `employee_id % 2 = 1` to determine if the `employee_id` is odd.
2. **Name Check**: Use `name NOT LIKE 'M%'` to ensure the name does not start with `'M'`.
3. **Bonus Calculation**:
   - If both conditions are true, the `bonus = salary`.
   - Otherwise, `bonus = 0`.
4. **Order by employee_id**: Ensure the result is sorted by `employee_id` as required.

---

# Solution

```sql
SELECT 
    employee_id,
    IF(employee_id % 2 = 1 AND name NOT LIKE 'M%', salary, 0) AS bonus
FROM 
    Employees
ORDER BY 
    employee_id;
```

---

# Explanation

1. **`IF(employee_id % 2 = 1 AND name NOT LIKE 'M%', salary, 0)`**:
   - Checks if `employee_id` is odd (`employee_id % 2 = 1`).
   - Ensures the name does not start with `'M'` (`name NOT LIKE 'M%'`).
   - Returns the `salary` as `bonus` if both conditions are true; otherwise, it returns `0`.

2. **`ORDER BY employee_id`**:
   - Ensures the result is sorted by `employee_id` in ascending order.

---

# Key Takeaways

- **Conditional Logic**: The `IF` function in SQL is useful for conditionally assigning values based on logical checks.
- **String Matching**: The `LIKE` operator is a powerful tool for matching patterns in strings.
- **Sorting**: Always use `ORDER BY` to control the order of the output.
