###  Classifying Triangles by Lengths

Table: Triangles

```plaintext
+-------------+------+ 
| Column Name | Type | 
+-------------+------+ 
| A           | int  | 
| B           | int  |
| C           | int  |
+-------------+------+
(A, B, C) is the primary key for this table.
Each row includes the lengths of each of a triangle's three sides.
```

Write a query to find the type of triangle. Output one of the following for each row:

- **Equilateral**: It's a triangle with 3 sides of equal length.
- **Isosceles**: It's a triangle with 2 sides of equal length.
- **Scalene**: It's a triangle with 3 sides of differing lengths.
- **Not A Triangle**: The given values of A, B, and C don't form a triangle.

Return the result table in any order.

The result format is in the following example.

### Example 1:

Input: 

```plaintext
Triangles table:
+----+----+----+
| A  | B  | C  |
+----+----+----+
| 20 | 20 | 23 |
| 20 | 20 | 20 |
| 20 | 21 | 22 |
| 13 | 14 | 30 |
+----+----+----+
```

Output: 

```plaintext
+----------------+
| triangle_type  | 
+----------------+
| Isosceles      | 
| Equilateral    |
| Scalene        |
| Not A Triangle |
+----------------+
```

### Explanation:

- Values in the first row form an **Isosceles triangle**, because A = B.
- Values in the second row form an **Equilateral triangle**, because A = B = C.
- Values in the third row form a **Scalene triangle**, because A != B != C.
- Values in the fourth row cannot form a triangle, because the combined value of sides A and B is not larger than that of side C.

---

### Thought Process:

1. **Understand Triangle Formation Rules**:
   - The sum of any two sides must be greater than the third side for the values to form a triangle.
   - If any two sides are equal and the third is different, it's an **Isosceles triangle**.
   - If all three sides are equal, it's an **Equilateral triangle**.
   - If all three sides are different, it's a **Scalene triangle**.
   - If the triangle formation rule is violated, it is **Not A Triangle**.

2. **Break Down the Logic**:
   - Use a `CASE` statement to determine the type of triangle.
   - First, check if the sides fail the triangle formation rule.
   - Then, classify as **Equilateral**, **Isosceles**, or **Scalene**.

3. **Order of Conditions**:
   - Start with the rule for **Not A Triangle**.
   - Then check for **Equilateral**, followed by **Isosceles**, and finally **Scalene**.

---

### Query:

```sql
SELECT 
  CASE
    WHEN (A + B <= C) OR (B + C <= A) OR (A + C <= B) THEN 'Not A Triangle'
    WHEN (A = B AND B = C) THEN 'Equilateral'
    WHEN (A = B AND B != C) OR (A = C AND A != B) OR (B = C AND A != B) THEN 'Isosceles'
    ELSE 'Scalene'
  END AS triangle_type
FROM Triangles;
```

---

### Explanation of the Query:

1. **`CASE` Statement**:
   - **First Condition**: Check if the sides violate the triangle formation rule:
     ```sql
     (A + B <= C) OR (B + C <= A) OR (A + C <= B)
     ```
     If true, label it as `'Not A Triangle'`.
   - **Second Condition**: Check for **Equilateral triangle**:
     ```sql
     (A = B AND B = C)
     ```
   - **Third Condition**: Check for **Isosceles triangle**:
     ```sql
     (A = B AND B != C) OR (A = C AND A != B) OR (B = C AND A != B)
     ```
   - **Else Condition**: If none of the above, classify as `'Scalene'`.

2. **`FROM` Clause**: Retrieve data from the `Triangles` table.

---

### Output:

Using the input example:

```plaintext
+----------------+
| triangle_type  | 
+----------------+
| Isosceles      | 
| Equilateral    |
| Scalene        |
| Not A Triangle |
+----------------+
