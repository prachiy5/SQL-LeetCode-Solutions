# Find Top Performing Driver

You are given three tables:

### Drivers Table
| Column Name  | Type    |
|--------------|---------|
| driver_id    | int     |
| name         | varchar |
| age          | int     |
| experience   | int     |
| accidents    | int     |

- `driver_id` is the unique key for this table.
- Each row includes a driver's ID, their name, age, years of driving experience, and the number of accidents they’ve had.

### Vehicles Table
| Column Name  | Type    |
|--------------|---------|
| vehicle_id   | int     |
| driver_id    | int     |
| model        | varchar |
| fuel_type    | varchar |
| mileage      | int     |

- `(vehicle_id, driver_id, fuel_type)` is the unique key for this table.
- Each row includes the vehicle's ID, the driver who operates it, the model, fuel type, and mileage.

### Trips Table
| Column Name  | Type    |
|--------------|---------|
| trip_id      | int     |
| vehicle_id   | int     |
| distance     | int     |
| duration     | int     |
| rating       | int     |

- `trip_id` is the unique key for this table.
- Each row includes a trip's ID, the vehicle used, the distance covered (in miles), the trip duration (in minutes), and the passenger's rating (1-5).

### Task
Uber is analyzing drivers based on their trips. Write a solution to find the top-performing driver for each fuel type based on the following criteria:

1. A driver's performance is calculated as the **average rating** across all their trips, rounded to 2 decimal places.
2. If two drivers have the same average rating, the driver with the **longer total distance traveled** should be ranked higher.
3. If there is still a tie, choose the driver with the **fewest accidents**.
4. Return the result table ordered by `fuel_type` in ascending order.

### Input Example
#### Drivers Table:
| driver_id | name     | age | experience | accidents |
|-----------|----------|-----|------------|-----------|
| 1         | Alice    | 34  | 10         | 1         |
| 2         | Bob      | 45  | 20         | 3         |
| 3         | Charlie  | 28  | 5          | 0         |

#### Vehicles Table:
| vehicle_id | driver_id | model   | fuel_type | mileage |
|------------|-----------|---------|-----------|---------|
| 100        | 1         | Sedan   | Gasoline  | 20000   |
| 101        | 2         | SUV     | Electric  | 30000   |
| 102        | 3         | Coupe   | Gasoline  | 15000   |

#### Trips Table:
| trip_id | vehicle_id | distance | duration | rating |
|---------|------------|----------|----------|--------|
| 201     | 100        | 50       | 30       | 5      |
| 202     | 100        | 30       | 20       | 4      |
| 203     | 101        | 100      | 60       | 4      |
| 204     | 101        | 80       | 50       | 5      |
| 205     | 102        | 40       | 30       | 5      |
| 206     | 102        | 60       | 40       | 5      |

### Output Example
| fuel_type | driver_id | rating | distance |
|-----------|-----------|--------|----------|
| Electric  | 2         | 4.50   | 180      |
| Gasoline  | 3         | 5.00   | 100      |

### Explanation
1. **Fuel Type: Gasoline**:
   - Alice (Driver 1): Average rating = `(5 + 4) / 2 = 4.50`, Total distance = `50 + 30 = 80`.
   - Charlie (Driver 3): Average rating = `(5 + 5) / 2 = 5.00`, Total distance = `40 + 60 = 100`.
   - Charlie is selected because they have the highest average rating.

2. **Fuel Type: Electric**:
   - Bob (Driver 2): Average rating = `(4 + 5) / 2 = 4.50`, Total distance = `100 + 80 = 180`.
   - Bob is selected as the only driver for Electric fuel type.

### Thought Process
1. **Join Tables**:
   - Combine `Drivers`, `Vehicles`, and `Trips` tables to associate trips with drivers and their respective fuel types.

2. **Calculate Metrics**:
   - Compute the average rating (`avg_rating`), total distance (`total_distance`), and total accidents for each driver grouped by fuel type.

3. **Rank Drivers**:
   - Use `DENSE_RANK()` to rank drivers within each fuel type based on:
     - `avg_rating` (descending).
     - `total_distance` (descending).
     - `total_accidents` (ascending).

4. **Select Top Drivers**:
   - Filter the top-ranked driver for each fuel type.

5. **Order Results**:
   - Sort the final result by `fuel_type` in ascending order.

### Query
```sql
WITH cte AS (
    SELECT 
        d.driver_id,
        name,
        v.vehicle_id,
        fuel_type,
        rating,
        distance,
        accidents
    FROM 
        drivers d
    INNER JOIN 
        vehicles v ON d.driver_id = v.driver_id
    INNER JOIN 
        trips t ON t.vehicle_id = v.vehicle_id
),

cte2 AS (
    SELECT 
        driver_id,
        fuel_type,
        SUM(rating) / COUNT(rating) AS avg_rating,
        SUM(distance) AS total_distance,
        SUM(accidents) AS total_accidents
    FROM 
        cte
    GROUP BY 
        fuel_type, driver_id
),

cte3 AS (
    SELECT 
        *,
        DENSE_RANK() OVER (
            PARTITION BY fuel_type 
            ORDER BY avg_rating DESC, total_distance DESC, total_accidents ASC
        ) AS r
    FROM 
        cte2
)

SELECT 
    fuel_type,
    driver_id,
    ROUND(avg_rating, 2) AS rating,
    total_distance AS distance
FROM 
    cte3
WHERE 
    r = 1;
