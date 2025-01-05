### Activity Participants
We are given two tables:

#### Table: `Friends`

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |
| activity    | varchar |

- `id` is the primary key.
- Each row represents a friend and the activity they participate in.

#### Table: `Activities`

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key.
- Each row represents an activity.

### Task
Write a SQL query to find the names of all activities that have neither the maximum nor the minimum number of participants. Each activity in the `Activities` table is performed by any person in the `Friends` table.

### Example
#### Input:
**Friends table:**

| id | name        | activity     |
|----|-------------|--------------|
| 1  | Jonathan D. | Eating       |
| 2  | Jade W.     | Singing      |
| 3  | Victor J.   | Singing      |
| 4  | Elvis Q.    | Eating       |
| 5  | Daniel A.   | Eating       |
| 6  | Bob B.      | Horse Riding |

**Activities table:**

| id | name         |
|----|--------------|
| 1  | Eating       |
| 2  | Singing      |
| 3  | Horse Riding |

#### Output:

| activity |
|----------|
| Singing  |

#### Explanation:
- **Eating:** 3 participants (Jonathan D., Elvis Q., Daniel A.)
- **Horse Riding:** 1 participant (Bob B.)
- **Singing:** 2 participants (Victor J., Jade W.)

The activity `Eating` has the maximum participants (3), and `Horse Riding` has the minimum participants (1). Therefore, `Singing` is the only activity that qualifies.

---

### Thought Process
1. **Count Participants for Each Activity:**
   - Group the `Friends` table by `activity` and count the number of participants for each activity.

2. **Identify Activities with Maximum and Minimum Participants:**
   - Use `MIN()` and `MAX()` on the count of participants to find the maximum and minimum participant counts.

3. **Exclude Maximum and Minimum:**
   - Filter out activities with participant counts equal to the maximum or minimum values.

4. **Return Remaining Activities:**
   - Select the `activity` names that satisfy the above conditions.

---

### SQL Query
```sql
WITH cte AS (
    SELECT COUNT(id) AS total, activity
    FROM friends
    GROUP BY activity
)

SELECT activity 
FROM cte
WHERE total NOT IN (SELECT MIN(total) FROM cte)
  AND total NOT IN (SELECT MAX(total) FROM cte);
```

---

### Key Takeaways
1. **Grouping and Aggregation:** Use `GROUP BY` and `COUNT()` to calculate the number of participants for each activity.
2. **Filter with Subqueries:** Use subqueries to find the maximum and minimum participant counts and exclude them from the result.
3. **Clean Output:** Return only the activity names that meet the criteria.

### Related Topics
- SQL Aggregations
- Filtering with Subqueries
- Common Table Expressions (CTEs)
