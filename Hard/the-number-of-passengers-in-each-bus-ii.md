 ## The Number of Passengers in Each Bus II

 We are given two tables:

 **Table: Buses**

 | Column Name  | Type    |
 |--------------|---------|
 | bus_id       | int     |
 | arrival_time | int     |
 | capacity     | int     |
 
 Each row in this table represents a bus with a unique ID (`bus_id`), its arrival time at the station (`arrival_time`), and the number of passengers it can hold (`capacity`).

 **Table: Passengers**

 | Column Name  | Type    |
 |--------------|---------|
 | passenger_id | int     |
 | arrival_time | int     |
 
 Each row in this table represents a passenger with a unique ID (`passenger_id`) and the time they arrived at the station (`arrival_time`).

 The task is to determine the number of passengers that board each bus. The boarding conditions are:

 1. A bus can pick up only those passengers who arrived at or before the bus's arrival time.
 2. A bus can take at most `capacity` passengers.
 3. Passengers board buses in the order of bus arrival times.

 The result table should include:

 | Column Name     | Description                  |
 |-----------------|------------------------------|
 | bus_id          | The ID of the bus.           |
 | passengers_cnt  | The number of passengers who boarded this bus. |

 ## Example Input and Output

**Input:**

 **Buses table:**
 | bus_id | arrival_time | capacity |
 |--------|--------------|----------|
 | 1      | 2            | 1        |
 | 2      | 4            | 10       |
 | 3      | 7            | 2        |

 **Passengers table:**
 | passenger_id | arrival_time |
 |--------------|--------------|
 | 11           | 1            |
 | 12           | 1            |
 | 13           | 5            |
 | 14           | 6            |
 | 15           | 7            |

 **Output:**
 | bus_id | passengers_cnt |
 |--------|----------------|
 | 1      | 1              |
 | 2      | 1              |
 | 3      | 2              |

 ## Thought Process

 The solution involves matching passengers to buses under the given constraints. We will:
 1. Combine passenger and bus data to determine potential matches based on arrival times.
 2. Rank passengers for each bus to ensure proper order.
 3. Use a recursive CTE to calculate the number of passengers boarding each bus and the remaining passengers for the next buses.

 ## SQL Solution
```
WITH RECURSIVE cte AS (
    -- Combine buses and passengers, ensuring passengers arrive on or before the bus
    SELECT 
        b.bus_id,
        b.capacity,
        COUNT(p.passenger_id) AS p_count, 
        ROW_NUMBER() OVER(ORDER BY b.arrival_time) AS rnk  
    FROM Buses b
    LEFT JOIN Passengers p ON b.arrival_time >= p.arrival_time
    GROUP BY b.bus_id, b.capacity
),

cte2 AS (
    -- Calculate new passengers for each bus compared to the previous bus
    SELECT 
        bus_id,
        capacity,
        IFNULL(p_count - LAG(p_count) OVER(ORDER BY rnk), p_count) AS diff,  
        rnk
    FROM cte
),

cte3 AS (
    -- Recursive CTE to calculate passengers boarding each bus and those remaining
    -- Base case: For the first bus, calculate boarded and remaining passengers
    SELECT 
        bus_id, 
        LEAST(capacity, diff) AS boarded,  
        GREATEST(0, diff - capacity) AS remain,  
        rnk
    FROM cte2
    WHERE rnk = 1

    UNION ALL

    -- Recursive case: Handle remaining passengers for subsequent buses
    SELECT 
        c.bus_id, 
        LEAST(c.capacity, c.diff + t.remain) AS boarded,  
        GREATEST(0, c.diff + t.remain - c.capacity) AS remain,  
        c.rnk
    FROM cte2 c
    JOIN cte3 t ON c.rnk = t.rnk + 1 
)

-- Final selection to output bus_id and number of boarded passengers
SELECT 
    bus_id, 
    boarded AS passengers_cnt  
FROM cte3
ORDER BY bus_id;
```
