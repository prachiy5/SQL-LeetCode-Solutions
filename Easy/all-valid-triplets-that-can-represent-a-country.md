# All Valid Triplets That Can Represent a Country

### Problem

#### Tables

**SchoolA**:

| Column Name   | Type    |
|---------------|---------|
| student_id    | int     |
| student_name  | varchar |

- `student_id` is unique.
- Contains student names and IDs for School A.

**SchoolB**:

| Column Name   | Type    |
|---------------|---------|
| student_id    | int     |
| student_name  | varchar |

- `student_id` is unique.
- Contains student names and IDs for School B.

**SchoolC**:

| Column Name   | Type    |
|---------------|---------|
| student_id    | int     |
| student_name  | varchar |

- `student_id` is unique.
- Contains student names and IDs for School C.

#### Task

Find all possible triplets of students such that:
- **member_A** is from `SchoolA`.
- **member_B** is from `SchoolB`.
- **member_C** is from `SchoolC`.
- The selected students have **distinct names and IDs**.

### Example

#### Input

**SchoolA** table:

| student_id | student_name |
|------------|--------------|
| 1          | Alice        |
| 2          | Bob          |

**SchoolB** table:

| student_id | student_name |
|------------|--------------|
| 3          | Tom          |

**SchoolC** table:

| student_id | student_name |
|------------|--------------|
| 3          | Tom          |
| 2          | Jerry        |
| 10         | Alice        |

#### Output

| member_A | member_B | member_C |
|----------|----------|----------|
| Alice    | Tom      | Jerry    |
| Bob      | Tom      | Alice    |

#### Explanation

1. **(Alice, Tom, Tom):** Rejected, as `member_B` and `member_C` have the same name and ID.
2. **(Alice, Tom, Jerry):** Valid, as all names and IDs are distinct.
3. **(Alice, Tom, Alice):** Rejected, as `member_A` and `member_C` share the same name.
4. **(Bob, Tom, Tom):** Rejected, as `member_B` and `member_C` have the same name and ID.
5. **(Bob, Tom, Jerry):** Rejected, as `member_A` and `member_C` share the same ID.
6. **(Bob, Tom, Alice):** Valid, as all names and IDs are distinct.

---

### Thought Process

1. **Understand Constraints:**
   - Students must have distinct `student_id` and `student_name`.
   - Ensure all three members come from different schools.

2. **Join Tables:**
   - Use `INNER JOIN` to combine `SchoolA`, `SchoolB`, and `SchoolC`.

3. **Apply Distinct Conditions:**
   - `A.student_id != B.student_id` and `A.student_name != B.student_name`.
   - `A.student_id != C.student_id`, `B.student_id != C.student_id`.
   - `A.student_name != C.student_name`, `B.student_name != C.student_name`.

4. **Select Valid Triplets:**
   - Select the names of students from each school as `member_A`, `member_B`, and `member_C`.

---

### Solution

```sql
SELECT 
    A.student_name AS member_A, 
    B.student_name AS member_B, 
    C.student_name AS member_C
FROM 
    SchoolA A
INNER JOIN 
    SchoolB B
    ON A.student_id != B.student_id 
    AND A.student_name != B.student_name
INNER JOIN 
    SchoolC C
    ON A.student_id != C.student_id 
    AND B.student_id != C.student_id
    AND A.student_name != C.student_name 
    AND B.student_name != C.student_name;
```

---

### Solution Explanation

1. **Join SchoolA and SchoolB:**
   - Ensure `student_id` and `student_name` are distinct between the two schools.

2. **Join SchoolC:**
   - Further ensure `student_id` and `student_name` are distinct from both SchoolA and SchoolB.

3. **Select Valid Triplets:**
   - Extract the names of students as `member_A`, `member_B`, and `member_C`.

---

### Key Takeaways

- Use `INNER JOIN` to enforce constraints across multiple tables.
- Combine multiple conditions to ensure distinct values.
- Always test queries with different inputs to confirm validity.

---

### Related Topics

- SQL Joins
- Filtering Rows with Conditions
- Query Optimization
