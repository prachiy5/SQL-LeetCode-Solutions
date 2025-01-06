### Build the Equation

Table: Terms
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| power       | int  |
| factor      | int  |
+-------------+------+
```
power is the column with unique values for this table.
Each row of this table contains information about one term of the equation.
power is an integer in the range [0, 100].
factor is an integer in the range [-100, 100] and cannot be zero.

You have a very powerful program that can solve any equation of one variable in the world. The equation passed to the program must be formatted as follows:

1. The left-hand side (LHS) should contain all the terms.
2. The right-hand side (RHS) should be zero.
3. Each term of the LHS should follow the format "<sign><fact>X^<pow>" where:
    - `<sign>` is either "+" or "-".
    - `<fact>` is the absolute value of the factor.
    - `<pow>` is the value of the power.
4. If the power is 1, do not add "^<pow>". For example, if power = 1 and factor = 3, the term will be "+3X".
5. If the power is 0, add neither "X" nor "^<pow>". For example, if power = 0 and factor = -3, the term will be "-3".
6. The powers in the LHS should be sorted in descending order.

Write a solution to build the equation.

The result format is in the following example.

#### Example 1:

**Input:** 

Terms table:
```
+-------+--------+
| power | factor |
+-------+--------+
| 2     | 1      |
| 1     | -4     |
| 0     | 2      |
+-------+--------+
```
**Output:** 
```
+--------------+
| equation     |
+--------------+
| +1X^2-4X+2=0 |
+--------------+
```
#### Example 2:

**Input:** 

Terms table:
```
+-------+--------+
| power | factor |
+-------+--------+
| 4     | -4     |
| 2     | 1      |
| 1     | -1     |
+-------+--------+
```
**Output:** 
```
+-----------------+
| equation        |
+-----------------+
| -4X^4+1X^2-1X=0 |
+-----------------+
```
#### Follow-Up:

What will be changed in your solution if the power is not a primary key but each power should be unique in the answer?

---

### Thought Process

1. **Objective:** Build a formatted equation string from the terms in the `Terms` table, ensuring all terms are sorted in descending order of power.

2. **Breaking Down the Problem:**
   - Each term has a `power` and a `factor`.
   - Based on the value of `power`, different formatting is applied:
     - If `power = 0`, the term is represented as "+<factor>" or "-<factor>".
     - If `power = 1`, the term is represented as "+<factor>X" or "-<factor>X".
     - If `power > 1`, the term is represented as "+<factor>X^<power>" or "-<factor>X^<power>".
   - Combine all terms into a single string, sort them in descending order of `power`, and append "=0" at the end.

3. **Handling Edge Cases:**
   - Ensure the result string does not contain any extra separators.
   - Handle negative and positive factors correctly.
   - Use `GROUP_CONCAT` to concatenate terms in order.

4. **Follow-Up Adjustment:**
   - If the `power` is not a primary key but must still be unique, apply `DISTINCT` or a similar method to ensure duplicate powers are removed during query execution.

---

### SQL Query

```sql
WITH eqn_reps AS (
    SELECT 
        t.power,
        CONCAT(
            (CASE WHEN t.factor > 0 THEN '+' ELSE '-' END),
            (CASE 
                WHEN t.power > 1 THEN CONCAT(ABS(t.factor), 'X^', t.power)
                WHEN t.power = 1 THEN CONCAT(ABS(t.factor), 'X')
                ELSE ABS(t.factor)
             END)
        ) AS eqn_rep
    FROM Terms t
)

SELECT 
    CONCAT(GROUP_CONCAT(e.eqn_rep ORDER BY e.power DESC SEPARATOR ''), '=0') AS equation
FROM eqn_reps e;
