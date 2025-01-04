# Find Classes with at Least Five Students

### Problem

#### Table: Courses

| Column Name | Type    |
|-------------|---------|
| student     | varchar |
| class       | varchar |

- `(student, class)` is the primary key, meaning each combination of `student` and `class` is unique.
- Each row indicates the name of a student and the class they are enrolled in.

#### Task
Write a solution to find all the classes that have at least five students.

Return the result table in any order.

#### Example

**Input:**

`Courses` table:

| student | class    |
|---------|----------|
| A       | Math     |
| B       | English  |
| C       | Math     |
| D       | Biology  |
| E       | Math     |
| F       | Computer |
| G       | Math     |
| H       | Math     |
| I       | Math     |

**Output:**

| class   |
|---------|
| Math    |

**Explanation:**
- Math has 6 students, so it is included in the output.
- English, Biology, and Computer each have only 1 student, so they are not included.

---

### Thought Process

To solve this problem, the goal is to identify classes with at least five students. Here’s how I approached it step by step:

1. **Group by Class:**
   - Use the `GROUP BY` clause to group rows by `class`, treating all students in the same class as one group.

2. **Count Students in Each Class:**
   - Use the `COUNT()` function to calculate the total number of students in each class.

3. **Filter Classes with at Least Five Students:**
   - Use the `HAVING` clause to filter groups where the student count is greater than or equal to 5.

4. **Select Class Names:**
   - Return only the `class` column for the filtered groups.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT class
FROM Courses
GROUP BY class
HAVING COUNT(student) >= 5;
```

---

### Solution Explanation

- **GROUP BY Clause:**
  - Groups all rows by the `class` column, creating a separate group for each class.

- **COUNT(student):**
  - Counts the number of rows (students) in each group (class).

- **HAVING Clause:**
  - Filters out groups where the student count is less than 5.

- **SELECT Statement:**
  - Returns the `class` names that meet the condition.

---

### Key Takeaways

- **GROUP BY + HAVING:** The `HAVING` clause is used to filter grouped data based on aggregate functions like `COUNT()`.
- **COUNT Function:** Useful for calculating the size of each group.
- **Column Selection:** Select only the relevant columns to keep the output clean and focused.

---

### Related Topics
- GROUP BY Clause in SQL
- Filtering Groups with HAVING
- Aggregate Functions (COUNT, SUM, AVG)
