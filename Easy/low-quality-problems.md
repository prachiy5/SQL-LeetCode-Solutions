# Low Quality Problems

### Problem Description

You are tasked with finding low-quality problems from the `Problems` table. A problem is considered low-quality if its like percentage (calculated as the number of likes divided by the total votes, i.e., likes + dislikes) is strictly less than 60%. The result should return the `problem_id` of these problems in ascending order.

---

### Input Example

**Problems Table:**

| problem_id | likes | dislikes |
|------------|-------|----------|
| 6          | 1290  | 425      |
| 11         | 2677  | 8659     |
| 1          | 4446  | 2760     |
| 7          | 8569  | 6086     |
| 13         | 2050  | 4164     |
| 10         | 9002  | 7446     |

---

### Output Example

**Result Table:**

| problem_id |
|------------|
| 7          |
| 10         |
| 11         |
| 13         |

---

### Thought Process

1. **Understand the Like Percentage Formula:**
   - Calculate the like percentage as `likes / (likes + dislikes)`.
   - Multiply by 100 to convert it to a percentage.
2. **Filter Low-Quality Problems:**
   - A problem is low-quality if its like percentage is strictly less than 60%.
3. **Result Order:**
   - Return the `problem_id` of low-quality problems in ascending order.

---

### MySQL Query

```sql
WITH cte AS (
    SELECT 
        problem_id, 
        (likes / (likes + dislikes)) * 100 AS like_percentage
    FROM Problems
)
SELECT 
    problem_id
FROM 
    cte
WHERE 
    like_percentage < 60
ORDER BY 
    problem_id;
```

---

### Explanation of the Query

1. **Common Table Expression (CTE):**
   - Calculate the `like_percentage` for each problem using the formula `(likes / (likes + dislikes)) * 100`.
   - Select the `problem_id` and `like_percentage` for further filtering.
2. **Filter Low-Quality Problems:**
   - Use the `WHERE` clause to filter rows where `like_percentage < 60`.
3. **Result Sorting:**
   - Use `ORDER BY problem_id` to return the result in ascending order of `problem_id`.

---

### Key Takeaways

- **Mathematical Calculation:** The like percentage is derived from dividing the number of likes by the total votes.
- **Filtering:** Problems with a like percentage below the threshold (60%) are considered low-quality.
- **Ordering:** Sorting ensures the result is returned in the desired order.

---
