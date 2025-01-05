### Find Peak Calling Hours for Each City

We are given a table:

#### Table: Calls
| Column Name  | Type     |
|--------------|----------|
| caller_id    | int      |
| recipient_id | int      |
| call_time    | datetime |
| city         | varchar  |

- `(caller_id, recipient_id, call_time)` is the primary key.
- Each row contains:
  - `caller_id`: ID of the caller.
  - `recipient_id`: ID of the recipient.
  - `call_time`: Timestamp of the call.
  - `city`: The city where the call originated.

#### Task
Write a query to determine the peak calling hour for each city. A **peak calling hour** is defined as the hour during which the maximum number of calls occurred in that city. If multiple hours have the same number of calls, all of those hours should be included.

The result should include:
- `city`: The name of the city.
- `peak_calling_hour`: The hour of the peak calling period.
- `number_of_calls`: The total number of calls during that hour.

Return the result ordered by `peak_calling_hour` and `city` in descending order.

---

### Example Input/Output

##### Input:
**Calls table:**
| caller_id | recipient_id | call_time           | city     |
|-----------|--------------|---------------------|----------|
| 8         | 4            | 2021-08-24 22:46:07 | Houston  |
| 4         | 8            | 2021-08-24 22:57:13 | Houston  |
| 5         | 1            | 2021-08-11 21:28:44 | Houston  |
| 8         | 3            | 2021-08-17 22:04:15 | Houston  |
| 11        | 3            | 2021-08-17 13:07:00 | New York |
| 8         | 11           | 2021-08-17 14:22:22 | New York |

##### Output:
| city     | peak_calling_hour | number_of_calls |
|----------|-------------------|-----------------|
| Houston  | 22                | 3               |
| New York | 14                | 1               |
| New York | 13                | 1               |

##### Explanation:
- **Houston:**
  - Hour 22:00 has the highest call count (3 calls).
  - Hour 21:00 has fewer calls, so it is not a peak hour.
- **New York:**
  - Hour 13:00 and 14:00 both have 1 call each, making them peak hours.

The result is sorted by `peak_calling_hour` (descending) and `city` (descending).

---

### Thought Process
1. **Extract Hourly Data:**
   - Use the `HOUR()` function to extract the hour from the `call_time` column.
2. **Count Calls per Hour per City:**
   - Group by `city` and the extracted hour.
   - Count the number of calls for each group.
3. **Rank Peak Hours:**
   - Use the `DENSE_RANK()` function to rank hours within each city based on the call count, in descending order.
4. **Filter Peak Hours:**
   - Select only the rows where the rank is 1 (highest call count).
5. **Order Results:**
   - Order the output by `peak_calling_hour` and `city`, both in descending order.

---

### Query
```sql
WITH cte AS (
    SELECT city,
           HOUR(call_time) AS peak_calling_hour,
           COUNT(*) AS number_of_calls,
           DENSE_RANK() OVER (
               PARTITION BY city 
               ORDER BY COUNT(*) DESC
           ) AS n_calls
    FROM calls
    GROUP BY city, HOUR(call_time)
)
 
SELECT city,
       peak_calling_hour,
       number_of_calls
FROM cte
WHERE n_calls = 1
ORDER BY peak_calling_hour DESC, city DESC;
```

---

### Key Takeaways
- **Window Functions:**
  - `DENSE_RANK()` is used to rank hours by the number of calls for each city.
- **Time Functions:**
  - `HOUR(call_time)` extracts the hour from the timestamp to group calls by hour.
- **Conditional Filtering:**
  - Only rows with the highest rank (`n_calls = 1`) are selected as peak hours.
- **Ordering:**
  - Results are sorted by `peak_calling_hour` and `city` in descending order.

---

### Related Topics
- SQL Window Functions (`DENSE_RANK`)
- SQL Grouping and Aggregation
- Date and Time Functions in SQL
- Query Optimization with CTEs
