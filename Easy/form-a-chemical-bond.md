### Form a Chemical Bond

#### Table: Elements

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| symbol      | varchar |
| type        | enum    |
| electrons   | int     |
+-------------+---------+
```
- `symbol` is the primary key (column with unique values) for this table.
- Each row of this table contains information about one element.
- `type` is an ENUM (category) of type ('Metal', 'Nonmetal', 'Noble').
  - If `type` is `Noble`, `electrons` is `0`.
  - If `type` is `Metal`, `electrons` is the number of electrons that one atom of this element can give.
  - If `type` is `Nonmetal`, `electrons` is the number of electrons that one atom of this element needs.

Two elements can form a bond if one of them is `Metal` and the other is `Nonmetal`.

Write a solution to find all the pairs of elements that can form a bond.

Return the result table in any order.

#### The result format is in the following example:

**Example 1:**

**Input:**

`Elements` table:

```text
+--------+----------+-----------+
| symbol | type     | electrons |
+--------+----------+-----------+
| He     | Noble    | 0         |
| Na     | Metal    | 1         |
| Ca     | Metal    | 2         |
| La     | Metal    | 3         |
| Cl     | Nonmetal | 1         |
| O      | Nonmetal | 2         |
| N      | Nonmetal | 3         |
+--------+----------+-----------+
```

**Output:**

```text
+-------+----------+
| metal | nonmetal |
+-------+----------+
| La    | Cl       |
| Ca    | Cl       |
| Na    | Cl       |
| La    | O        |
| Ca    | O        |
| Na    | O        |
| La    | N        |
| Ca    | N        |
| Na    | N        |
+-------+----------+
```

**Explanation:**
- Metal elements are `La`, `Ca`, and `Na`.
- Nonmetal elements are `Cl`, `O`, and `N`.
- Each Metal element pairs with a Nonmetal element in the output table.

---

### Thought Process:

1. **Understand the Requirement:**
   - Identify which elements are `Metals` and which are `Nonmetals`.
   - Create pairs where one element is a `Metal` and the other is a `Nonmetal`.

2. **Key Observations:**
   - Noble elements do not participate as they cannot form bonds.
   - Use a Cartesian product (cross join) to pair every `Metal` with every `Nonmetal`.

3. **Steps to Approach:**
   - Create two subsets of the `Elements` table:
     - One containing only `Metal` elements.
     - One containing only `Nonmetal` elements.
   - Use a Cartesian join to pair all rows from the `Metal` subset with all rows from the `Nonmetal` subset.
   - Select the `symbol` column from both subsets to form the `metal` and `nonmetal` pairs.

4. **Return the Result:**
   - Return the pairs without any specific order.

---

### Query:

```sql
SELECT 
    a.symbol AS metal,
    b.symbol AS nonmetal
FROM 
    Elements AS a,
    Elements AS b
WHERE 
    a.type = 'Metal' AND b.type = 'Nonmetal';
```

---

### Explanation of the Query:

1. **Alias Usage:**
   - `a` is used as an alias for the `Metal` subset of the `Elements` table.
   - `b` is used as an alias for the `Nonmetal` subset of the `Elements` table.

2. **Cartesian Join:**
   - Every row in `a` is paired with every row in `b` to form all possible combinations.

3. **Filter Criteria:**
   - Use the `WHERE` clause to ensure `a` contains only `Metal` elements (`a.type = 'Metal'`) and `b` contains only `Nonmetal` elements (`b.type = 'Nonmetal'`).

4. **Select Columns:**
   - `a.symbol` is returned as `metal`.
   - `b.symbol` is returned as `nonmetal`.

---

### Why this Output?
- The Cartesian join pairs each `Metal` with every `Nonmetal`.
- The filtered output ensures only valid bonds (between `Metal` and `Nonmetal`) are included.
- Example pairs include:
  - `La` with `Cl`, `O`, and `N`.
  - `Ca` with `Cl`, `O`, and `N`.
  - `Na` with `Cl`, `O`, and `N`.

---

### Result:
The output table contains all valid `metal`-`nonmetal` pairs.
