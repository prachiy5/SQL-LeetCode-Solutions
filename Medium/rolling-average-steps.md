### Rolling Average Steps

We are given a table:

#### Table: Steps
| Column Name | Type |
|-------------|------|
| user_id     | int  |
| steps_count | int  |
| steps_date  | date |

- `(user_id, steps_date)` is the primary key.
- Each row represents a user's step count on a specific date.

#### Task
Write a query to calculate the 3-day rolling average of steps for each user. The rolling average for a day is computed as the average of the step counts for that day and the two preceding days (if available). If there are fewer than 3 consecutive days of data, the rolling average is not defined for that day.

The output should include:
- `user_id`
- `steps_date`
- `rolling_average` (rounded to two decimal places)

The result table should be ordered by `user_id` and `steps_date` in ascending order.

---

### Example Input/Output

##### Input:
**Steps table:**
| user_id | steps_count | steps_date |
|---------|-------------|------------|
| 1       | 687         | 2021-09-02 |
| 1       | 395         | 2021-09-04 |
| 1       | 499         | 2021-09-05 |
| 1       | 712         | 2021-09-06 |
| 1       | 576         | 2021-09-07 |
| 2       | 153         | 2021-09-06 |
| 2       | 171         | 2021-09-07 |
| 2       | 530         | 2021-09-08 |
| 3       | 945         | 2021-09-04 |
| 3       | 120         | 2021-09-07 |
| 3       | 557         | 2021-09-08 |
| 3       | 840         | 2021-09-09 |
| 3       | 627         | 2021-09-10 |
| 5       | 382         | 2021-09-05 |
| 6       | 480         | 2021-09-01 |
| 6       | 191         | 2021-09-02 |
| 6       | 303         | 2021-09-05 |

##### Output:
| user_id | steps_date | rolling_average |
|---------|------------|-----------------|
| 1       | 2021-09-06 | 535.33          |
| 1       | 2021-09-07 | 595.67          |
| 2       | 2021-09-08 | 284.67          |
| 3       | 2021-09-09 | 505.67          |
| 3       | 2021-09-10 | 674.67          |

##### Explanation:
- For `user_id = 1, steps_date = 2021-09-06`:
  - Steps for the last 3 days: 395, 499, 712.
  - Rolling average: \((395 + 499 + 712) / 3 = 535.33\).
- For `user_id = 1, steps_date = 2021-09-07`:
  - Steps for the last 3 days: 499, 712, 576.
  - Rolling average: \((499 + 712 + 576) / 3 = 595.67\).
- For `user_id = 2, steps_date = 2021-09-08`:
  - Steps for the last 3 days: 153, 171, 530.
  - Rolling average: \((153 + 171 + 530) / 3 = 284.67\).
- Similar calculations apply to `user_id = 3`.

---

### Thought Process
1. **Calculate Rolling Average:**
   - Use a `WINDOW FUNCTION` to calculate the 3-day rolling average for each user.
   - Use `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` to include the current day and the two preceding days.
2. **Handle Missing Days:**
   - Ensure the rolling average is only computed when there are three consecutive days of data.
   - Use the `LAG()` function to check the date difference between the current day and the day two rows back.
3. **Filter Results:**
   - Only include rows where the rolling average is defined (i.e., when the date difference is exactly 2).
4. **Order Results:**
   - Return the result ordered by `user_id` and `steps_date` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT user_id, 
           steps_count,
           steps_date,
           ROUND(AVG(steps_count) OVER (
               PARTITION BY user_id 
               ORDER BY steps_date 
               ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
           ), 2) AS rolling_avg,
           LAG(steps_date, 2) OVER (
               PARTITION BY user_id 
               ORDER BY steps_date
           ) AS before_two_days
    FROM steps
)

SELECT user_id, 
       steps_date, 
       rolling_avg AS rolling_average
FROM cte
WHERE DATEDIFF(steps_date, before_two_days) = 2
ORDER BY user_id, steps_date;
```

---

### Key Takeaways
- **Window Functions:** `AVG()` computes the rolling average, and `LAG()` helps track preceding rows for date validation.
- **Conditional Filtering:** Use `DATEDIFF()` to ensure the rolling average is only computed for consecutive days.
- **Partitioning:** The `PARTITION BY` clause ensures calculations are grouped by `user_id`.
- **Result Formatting:** The rolling average is rounded to two decimal places for clarity.

---

### Related Topics
- SQL Window Functions
- Conditional Filtering
- Time-Based Aggregations
- Query Optimization with CTEs
