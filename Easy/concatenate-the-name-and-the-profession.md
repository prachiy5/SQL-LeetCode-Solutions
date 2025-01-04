
### Concatenate the Name and the Profession

**Table:** Person

| Column Name | Type    |
|-------------|---------|
| person_id   | int     |
| name        | varchar |
| profession  | ENUM    |

- `person_id` is the primary key (column with a unique value) for this table.
- Each row in this table contains a person's ID, name, and profession.
- The `profession` column is an ENUM of the type ('Doctor', 'Singer', 'Actor', 'Player', 'Engineer', 'Lawyer').

Write a solution to report each person's name followed by the first letter of their profession enclosed in parentheses.

Return the result table ordered by `person_id` in descending order.

The result format is shown in the following example.

#### Example:

**Input:**

Person table:

| person_id | name  | profession |
|-----------|-------|------------|
| 1         | Alex  | Singer     |
| 3         | Alice | Actor      |
| 2         | Bob   | Player     |
| 4         | Messi | Doctor     |
| 6         | Tyson | Engineer   |
| 5         | Meir  | Lawyer     |

**Output:**

| person_id | name     |
|-----------|----------|
| 6         | Tyson(E) |
| 5         | Meir(L)  |
| 4         | Messi(D) |
| 3         | Alice(A) |
| 2         | Bob(P)   |
| 1         | Alex(S)  |

**Explanation:**

Each person's name is followed by the first letter of their profession enclosed in parentheses. Note that there should not be any whitespace between the name and the parentheses. The result is ordered by `person_id` in descending order.

---

### Thought Process:

1. **Understand the Requirement:**
   - We need to create a formatted column that combines the person's name with the first letter of their profession enclosed in parentheses.
   - The result should be ordered by `person_id` in descending order.

2. **Approach:**
   - Use the `CONCAT` function to combine the `name` column with the formatted first letter of `profession`.
   - Use the `LEFT` function to extract the first letter of the `profession` column.
   - Use the `ORDER BY` clause to sort the results by `person_id` in descending order.

3. **Expected Output:**
   - The result table should contain two columns: `person_id` and `name` (formatted with the profession letter).

---

### Query:

```sql
SELECT 
    person_id, 
    CONCAT(name, '(', LEFT(profession, 1), ')') AS name
FROM 
    person
ORDER BY 
    person_id DESC;
```

---

### Explanation of the Query:

1. **`CONCAT(name, '(', LEFT(profession, 1), ')')`**:
   - Combines the `name` column with the first letter of the `profession` column enclosed in parentheses.
   - `LEFT(profession, 1)` extracts the first character from the `profession` column.

2. **`ORDER BY person_id DESC`**:
   - Ensures the result is sorted by `person_id` in descending order.

---

### Why this Output?

- The query correctly formats the `name` and appends the first letter of the profession as required.
- The descending order of `person_id` ensures the output matches the required format.

For example:
- `person_id = 6` becomes `Tyson(E)`.
- `person_id = 5` becomes `Meir(L)`.

---
