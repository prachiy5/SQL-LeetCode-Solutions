### Active Businesses
We are given a table:

#### Table: `Events`

| Column Name   | Type    |
|---------------|---------|
| business_id   | int     |
| event_type    | varchar |
| occurrences   | int     |

- `(business_id, event_type)` is the primary key, meaning each combination of `business_id` and `event_type` is unique.
- Each row logs information about the number of times (`occurrences`) an event of a specific type happened at a business.

### Definitions
1. **Average Activity**: The average occurrences of a specific `event_type` across all businesses.
2. **Active Business**: A business is considered active if it has more than one `event_type` where the `occurrences` are strictly greater than the **average activity** for that `event_type`.

### Task
Write a SQL query to find all active businesses. Return the result table with the column `business_id`. The result can be returned in any order.

### Example
#### Input:
**Events table:**

| business_id | event_type | occurrences |
|-------------|------------|-------------|
| 1           | reviews    | 7           |
| 3           | reviews    | 3           |
| 1           | ads        | 11          |
| 2           | ads        | 7           |
| 3           | ads        | 6           |
| 1           | page views | 3           |
| 2           | page views | 12          |

#### Output:

| business_id |
|-------------|
| 1           |

#### Explanation:
1. **Average Activity**:
   - `reviews`: (7+3)/2 = 5
   - `ads`: (11+7+6)/3 = 8
   - `page views`: (3+12)/2 = 7.5

2. **Active Businesses**:
   - `business_id = 1`:
     - `reviews`: 7 > 5 (greater than average)
     - `ads`: 11 > 8 (greater than average)
     - Since it has more than one `event_type` with `occurrences` > average, it is an active business.
   - `business_id = 2` and `business_id = 3` do not meet the criteria.

---

### Thought Process
1. **Calculate Average Activity**: Use a window function `AVG()` partitioned by `event_type` to calculate the average occurrences for each event type.
2. **Filter Businesses**: Identify rows where the `occurrences` are strictly greater than the calculated average for their respective `event_type`.
3. **Count Event Types**: Group by `business_id` and count the number of `event_type` entries that exceed the average. Filter businesses with more than one such event type.

---

### SQL Query
```sql
WITH cte AS (
    SELECT 
        *, 
        AVG(occurrences) OVER (PARTITION BY event_type) AS avg_event
    FROM Events
)

SELECT 
    business_id
FROM cte 
WHERE occurrences > avg_event
GROUP BY business_id
HAVING COUNT(event_type) > 1;
```

---

### Key Takeaways
1. Use `AVG()` as a window function to calculate averages across partitions (e.g., event types).
2. Use `WHERE` to filter rows where `occurrences` exceed the calculated average.
3. Use `HAVING` to filter groups of businesses that have more than one qualifying `event_type`.
4. Grouped results can be used for counting (`COUNT(event_type)`) and final filtering.

### Related Topics
- Window Functions
- Aggregation with `GROUP BY` and `HAVING`
- Conditional Filtering
