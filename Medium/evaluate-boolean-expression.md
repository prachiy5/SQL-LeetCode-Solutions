### Evaluate Boolean Expression
We are given two tables:

#### Table: `Variables`

| Column Name | Type    |
|-------------|---------|
| name        | varchar |
| value       | int     |

- `name` is the primary key.
- This table contains stored variables and their corresponding values.

#### Table: `Expressions`

| Column Name    | Type    |
|----------------|---------|
| left_operand   | varchar |
| operator       | enum    |
| right_operand  | varchar |

- `(left_operand, operator, right_operand)` is the primary key.
- This table contains boolean expressions that need to be evaluated.
- `operator` is an ENUM with values: `<`, `>`, `=`.
- The values of `left_operand` and `right_operand` are guaranteed to exist in the `Variables` table.

### Task
Evaluate the boolean expressions from the `Expressions` table using the values from the `Variables` table.

Return a result table with the following columns:
1. `left_operand`
2. `operator`
3. `right_operand`
4. `value`: A boolean result (`true` or `false`) for each expression.

Return the result table in any order.

### Example
#### Input:
**Variables table:**

| name | value |
|------|-------|
| x    | 66    |
| y    | 77    |

**Expressions table:**

| left_operand | operator | right_operand |
|--------------|----------|---------------|
| x            | >        | y             |
| x            | <        | y             |
| x            | =        | y             |
| y            | >        | x             |
| y            | <        | x             |
| x            | =        | x             |

#### Output:

| left_operand | operator | right_operand | value  |
|--------------|----------|---------------|--------|
| x            | >        | y             | false  |
| x            | <        | y             | true   |
| x            | =        | y             | false  |
| y            | >        | x             | true   |
| y            | <        | x             | false  |
| x            | =        | x             | true   |

#### Explanation:
1. For `x > y`, `66 > 77` is `false`.
2. For `x < y`, `66 < 77` is `true`.
3. For `x = y`, `66 = 77` is `false`.
4. For `y > x`, `77 > 66` is `true`.
5. For `y < x`, `77 < 66` is `false`.
6. For `x = x`, `66 = 66` is `true`.

---

### Thought Process
1. **Join the Tables:**
   - Join the `Expressions` table with the `Variables` table twice:
     - Once for the `left_operand` to get its value.
     - Once for the `right_operand` to get its value.

2. **Evaluate Conditions:**
   - Use a `CASE` statement to evaluate the expressions based on the `operator`.
   - Handle each operator (`<`, `>`, `=`) explicitly.

3. **Convert Boolean Values:**
   - Convert the boolean result (`true` or `false`) into the required format as strings.

4. **Select and Return Columns:**
   - Include all columns from `Expressions` and the evaluated `value`.

---

### SQL Query
```sql
WITH cte AS (
    SELECT e.*,
           CASE 
               WHEN operator = '>' THEN v1.value > v2.value
               WHEN operator = '<' THEN v1.value < v2.value
               ELSE v1.value = v2.value
           END AS value_num
    FROM Expressions e
    INNER JOIN Variables v1 ON v1.name = e.left_operand
    INNER JOIN Variables v2 ON v2.name = e.right_operand
)

SELECT 
    cte.left_operand,
    cte.operator,
    cte.right_operand,
    CASE 
        WHEN value_num IS TRUE THEN 'true'
        ELSE 'false'
    END AS value
FROM cte;
