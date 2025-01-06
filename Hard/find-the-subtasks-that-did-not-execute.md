
### Find the Subtasks That Did Not Execute
We are given two tables:
1. **Tasks** table containing task IDs and the total number of subtasks each task has.
2. **Executed** table containing task IDs and IDs of subtasks that were successfully executed.

Our objective is to identify the IDs of the subtasks that were **not executed** for each task.

### Input Example
#### Tasks Table:
| task_id | subtasks_count |
|---------|----------------|
| 1       | 3              |
| 2       | 2              |
| 3       | 4              |

#### Executed Table:
| task_id | subtask_id |
|---------|------------|
| 1       | 2          |
| 3       | 1          |
| 3       | 2          |
| 3       | 3          |
| 3       | 4          |

#### Expected Output:
| task_id | subtask_id |
|---------|------------|
| 1       | 1          |
| 1       | 3          |
| 2       | 1          |
| 2       | 2          |

### Explanation
- Task 1 has subtasks [1, 2, 3], and only subtask 2 is executed. Missing subtasks are 1 and 3.
- Task 2 has subtasks [1, 2], and none were executed. Both are missing.
- Task 3 has subtasks [1, 2, 3, 4], and all were executed. None are missing.

---

### Thought Process
To solve this problem:
1. **Generate All Subtasks:** For each task, generate all subtask IDs from 1 to `subtasks_count`.
2. **Identify Missing Subtasks:** Compare the generated subtasks with the `Executed` table and filter out the executed ones.
3. **Recursive CTE:** Use a recursive CTE to generate subtask IDs dynamically.

---

### SQL Query
```sql
WITH RECURSIVE cte AS (
    -- Base case: Start with the highest subtask count for each task
    SELECT task_id, subtasks_count
    FROM tasks
    
    UNION ALL

    -- Recursive case: Generate subtasks by decrementing subtasks_count until 1
    SELECT task_id, subtasks_count - 1
    FROM cte
    WHERE subtasks_count > 1
)

-- Select subtasks not present in the Executed table
SELECT 
    task_id, 
    subtasks_count AS subtask_id 
FROM cte
WHERE (task_id, subtasks_count) NOT IN (
    SELECT task_id, subtask_id 
    FROM executed
);
```

---

### Step-by-Step Explanation
1. **Recursive CTE (cte):**
   - **Base Case:** Starts with all tasks and their maximum `subtasks_count`.
   - **Recursive Case:** Decrements the `subtasks_count` by 1 for each task until it reaches 1.
   - This generates all possible subtask IDs for each task.

2. **Filter Missing Subtasks:**
   - Use a `NOT IN` clause to exclude subtasks that exist in the `Executed` table.

3. **Output:**
   - The result includes the `task_id` and the `subtask_id` for all missing subtasks.

---

### Key Points
- **Recursive CTE:** Efficiently generates subtasks without needing to hardcode ranges.
- **NOT IN Clause:** Filters out executed subtasks to identify the missing ones.
- **Dynamic Solution:** Works for any task-subtask combinations as long as `subtasks_count` is within the given constraints (2 <= subtasks_count <= 20).

---

### Final Output Example
For the given input, the query will produce:
| task_id | subtask_id |
|---------|------------|
| 1       | 1          |
| 1       | 3          |
| 2       | 1          |
| 2       | 2          |

This matches the expected result, ensuring correctness and clarity.
