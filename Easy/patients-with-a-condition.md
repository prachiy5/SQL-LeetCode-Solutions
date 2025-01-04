# Patients with a Condition

### Problem

#### Table: Patients

| Column Name  | Type    |
|--------------|---------|
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |

- `patient_id` is the primary key.
- The `conditions` column contains one or more medical condition codes separated by spaces.
- Type I Diabetes always starts with the `DIAB1` prefix.

### Task

Find the `patient_id`, `patient_name`, and `conditions` of patients who have Type I Diabetes. Return the results in any order.

### Example

#### Input

**Patients** table:

| patient_id | patient_name | conditions   |
|------------|--------------|--------------|
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |

#### Output

| patient_id | patient_name | conditions   |
|------------|--------------|--------------|
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |

#### Explanation

1. Bob and George both have a condition that starts with `DIAB1` in their `conditions` column.
2. Daniel, Alice, and Alain do not meet the criteria.

---

### Thought Process

1. **Understand the Criteria:**
   - The condition must start with `DIAB1`. This can appear:
     - At the beginning of the `conditions` column.
     - After a space in the `conditions` column.

2. **Use Pattern Matching:**
   - Use the `LIKE` operator to match patterns:
     - `"DIAB1%"`: Finds `DIAB1` at the beginning.
     - `"% DIAB1%"`: Finds `DIAB1` after a space.

3. **Filter Rows:**
   - Use a `WHERE` clause with `LIKE` conditions to filter rows that meet either pattern.

---

### Solution

```sql
SELECT *
FROM Patients
WHERE conditions LIKE "DIAB1%"
   OR conditions LIKE "% DIAB1%";
```

---

### Solution Explanation

1. **`LIKE "DIAB1%"`:**
   - Matches rows where the `conditions` column starts with `DIAB1`.

2. **`LIKE "% DIAB1%"`:**
   - Matches rows where `DIAB1` appears after a space in the `conditions` column.

3. **`WHERE` Clause:**
   - Filters rows that satisfy either of the two conditions.

4. **SELECT Statement:**
   - Retrieves all columns (`*`) for patients who meet the criteria.

---

### Key Takeaways

- Use `LIKE` with `%` for flexible pattern matching.
- Combine multiple conditions with `OR` to cover all possibilities.
- Ensure patterns handle spaces for multi-word conditions.

---

### Related Topics

- SQL Pattern Matching
- Filtering Rows with WHERE
- Handling String Data in SQL
