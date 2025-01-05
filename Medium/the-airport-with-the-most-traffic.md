### The Airport With the Most Traffic

We have a table `Flights` with the following schema:

```
+-------------------+------+ 
| Column Name       | Type | 
+-------------------+------+ 
| departure_airport | int  |
| arrival_airport   | int  |
| flights_count     | int  |
+-------------------+------+ 
```

- `(departure_airport, arrival_airport)` is the primary key.
- Each row indicates the number of flights (`flights_count`) departing from `departure_airport` and arriving at `arrival_airport`.

Our task is to identify the airport(s) with the most traffic. Traffic is defined as the total number of flights associated with an airport (both departures and arrivals).

If there are multiple airports with the highest traffic, we must return all of them.

### Input Example

**Flights table:**

| departure_airport | arrival_airport | flights_count |
|-------------------|-----------------|---------------|
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 2                 | 4               | 5             |

**Output:**

| airport_id |
|------------|
| 2          |

### Thought Process

1. **Understand Traffic Definition**: The total traffic for an airport is the sum of all flights departing from and arriving at that airport.
2. **Combine Departures and Arrivals**: Using a `UNION ALL` query, we can merge the departure and arrival columns into one column (`airport_id`) while preserving the `flights_count`.
3. **Aggregate Traffic per Airport**: Group by `airport_id` and compute the sum of `flights_count` for each airport.
4. **Rank by Traffic**: Use the `DENSE_RANK()` function to rank airports based on their total traffic in descending order.
5. **Filter for Maximum Traffic**: Select only those airports with the highest rank (i.e., rank = 1).
6. **Return Results**: Return the `airport_id` of the airports with the most traffic.

### SQL Query

```sql
WITH cte AS (
    SELECT departure_airport AS airport_id, flights_count FROM flights
    UNION ALL 
    SELECT arrival_airport AS airport_id, flights_count FROM flights
),

cte2 AS (
    SELECT 
        airport_id,
        SUM(flights_count) AS total_traffic,
        DENSE_RANK() OVER (ORDER BY SUM(flights_count) DESC) AS r
    FROM cte
    GROUP BY airport_id
)

SELECT airport_id 
FROM cte2
WHERE r = 1;
```

### Explanation of the Query

1. **Step 1: Combine Departures and Arrivals (`cte`)**:
   - Use `UNION ALL` to stack `departure_airport` and `arrival_airport` into a single column `airport_id` while retaining the `flights_count`.

2. **Step 2: Aggregate Traffic and Rank (`cte2`)**:
   - Group by `airport_id` and calculate the total flights (`SUM(flights_count)`) for each airport.
   - Use `DENSE_RANK()` to assign ranks to airports based on `total_traffic` in descending order.

3. **Step 3: Filter for Maximum Traffic**:
   - Select airports where the rank (`r`) is 1, representing the maximum traffic.

4. **Step 4: Return Results**:
   - Return the `airport_id` of the airports with the most traffic.

### Output Example

For the input:

| departure_airport | arrival_airport | flights_count |
|-------------------|-----------------|---------------|
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 2                 | 4               | 5             |

The query will produce:

| airport_id |
|------------|
| 2          |

### Why This Query Works

- The `UNION ALL` ensures both departures and arrivals contribute to the traffic count.
- `SUM(flights_count)` aggregates the total traffic for each airport.
- `DENSE_RANK()` ensures ties in traffic are appropriately handled.
- Filtering by `r = 1` guarantees we only retrieve airports with the maximum traffic.
