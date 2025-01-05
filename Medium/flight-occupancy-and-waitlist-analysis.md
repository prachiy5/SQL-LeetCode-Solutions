### Flight Occupancy and Waitlist Analysis

We are given two tables:

#### Table: Flights
| Column Name | Type |
|-------------|------|
| flight_id   | int  |
| capacity    | int  |

- `flight_id` contains unique values.
- Each row represents a flight and its capacity (number of available seats).

#### Table: Passengers
| Column Name  | Type |
|--------------|------|
| passenger_id | int  |
| flight_id    | int  |

- `passenger_id` contains unique values.
- Each row represents a passenger who has booked a ticket for a specific flight.

#### Task
Write a query to determine for each flight:
1. The number of passengers who successfully booked a flight (i.e., got a seat).
2. The number of passengers who are on the waitlist (i.e., no seats available).

The result should be ordered by `flight_id` in ascending order.

---

### Example Input/Output

##### Input:
**Flights table:**
| flight_id | capacity |
|-----------|----------|
| 1         | 2        |
| 2         | 2        |
| 3         | 1        |

**Passengers table:**
| passenger_id | flight_id |
|--------------|-----------|
| 101          | 1         |
| 102          | 1         |
| 103          | 1         |
| 104          | 2         |
| 105          | 2         |
| 106          | 3         |
| 107          | 3         |

##### Output:
| flight_id | booked_cnt | waitlist_cnt |
|-----------|------------|--------------|
| 1         | 2          | 1            |
| 2         | 2          | 0            |
| 3         | 1          | 1            |

##### Explanation:
- **Flight 1:**
  - Capacity: 2.
  - Passengers: 3.
  - 2 passengers successfully booked seats, and 1 is on the waitlist.
- **Flight 2:**
  - Capacity: 2.
  - Passengers: 2.
  - Both passengers successfully booked seats, and no one is on the waitlist.
- **Flight 3:**
  - Capacity: 1.
  - Passengers: 2.
  - 1 passenger successfully booked a seat, and 1 is on the waitlist.

---

### Thought Process
1. **Join the Tables:**
   - Perform a `LEFT JOIN` between the `Flights` and `Passengers` tables to associate passengers with their flights.
2. **Rank Passengers by Flight:**
   - Use the `ROW_NUMBER()` window function, partitioned by `flight_id`, to assign a rank (`row_no`) to each passenger within a flight.
3. **Classify Passengers:**
   - Use the rank (`row_no`) to classify passengers:
     - If `row_no` \( \leq \) `capacity`, the passenger is successfully booked.
     - If `row_no` > `capacity`, the passenger is on the waitlist.
4. **Count Passengers:**
   - Use conditional aggregation to count the number of booked and waitlisted passengers for each flight.
5. **Order Results:**
   - Group the results by `flight_id` and order them in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT f.flight_id, 
           f.capacity, 
           p.passenger_id,
           ROW_NUMBER() OVER (PARTITION BY f.flight_id ORDER BY p.passenger_id) AS row_no
    FROM flights f
    LEFT JOIN passengers p
    ON f.flight_id = p.flight_id
)

SELECT flight_id,
       COUNT(CASE WHEN row_no <= capacity THEN passenger_id END) AS booked_cnt,
       COUNT(CASE WHEN row_no > capacity THEN passenger_id END) AS waitlist_cnt
FROM cte
GROUP BY flight_id
ORDER BY flight_id;
```

---

### Key Takeaways
- **Window Functions:** `ROW_NUMBER()` is used to rank passengers within each flight, enabling us to identify who gets a seat and who is waitlisted.
- **Conditional Aggregation:** The `CASE` statement inside `COUNT()` allows us to classify and count passengers based on their rank.
- **LEFT JOIN:** Ensures that all flights are included in the result, even if there are no passengers.
- **Grouping:** Grouping by `flight_id` aggregates the booked and waitlisted counts for each flight.

---

### Related Topics
- SQL Window Functions
- Joins in SQL
- Conditional Aggregation
- Query Optimization with CTEs
