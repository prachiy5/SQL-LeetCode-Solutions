
### Hopper Company Queries II

You are tasked with finding the percentage of working drivers for each month of 2020. A driver is considered "working" if they accepted at least one ride during that month. You need to compute the working percentage, defined as:

working percentage = (Number of drivers who accepted rides\Total active drivers)*100

### Tables

**Drivers Table**

| Column Name | Type    |
|-------------|---------|
| driver_id   | int     |
| join_date   | date    |

- `driver_id` is the primary key.
- Each row contains a driver's ID and the date they joined the Hopper company.

**Rides Table**

| Column Name  | Type    |
|--------------|---------|
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |

- `ride_id` is the primary key.
- Each row contains a ride's ID, the user's ID that requested it, and the request date.
- Not all rides are guaranteed to be accepted.

**AcceptedRides Table**

| Column Name   | Type    |
|---------------|---------|
| ride_id       | int     |
| driver_id     | int     |
| ride_distance | int     |
| ride_duration | int     |

- `ride_id` is the primary key.
- Each row contains an accepted ride's ID, the driver who accepted the ride, and ride details such as distance and duration.

### Output

Return a table with the following:

| Column Name        | Type    |
|--------------------|---------|
| month              | int     |
| working_percentage | decimal |

- `month`: The month number (1 for January, 2 for February, ..., 12 for December).
- `working_percentage`: The percentage of working drivers, rounded to 2 decimal places. If no drivers are available, return `0.00`.
- Results should be ordered by `month` in ascending order.

### Example Input

**Drivers Table**

| driver_id | join_date  |
|-----------|------------|
| 10        | 2019-12-10 |
| 8         | 2020-01-13 |
| 5         | 2020-02-16 |
| 7         | 2020-03-08 |
| 4         | 2020-05-17 |
| 1         | 2020-10-24 |
| 6         | 2021-01-05 |

**Rides Table**

| ride_id | user_id | requested_at |
|---------|---------|--------------|
| 6       | 75      | 2019-12-09   |
| 1       | 54      | 2020-02-09   |
| 10      | 63      | 2020-03-04   |
| 19      | 39      | 2020-04-06   |
| 3       | 41      | 2020-06-03   |
| 13      | 52      | 2020-06-22   |
| 7       | 69      | 2020-07-16   |
| 17      | 70      | 2020-08-25   |
| 20      | 81      | 2020-11-02   |
| 5       | 57      | 2020-11-09   |
| 2       | 42      | 2020-12-09   |
| 11      | 68      | 2021-01-11   |

**AcceptedRides Table**

| ride_id | driver_id | ride_distance | ride_duration |
|---------|-----------|---------------|---------------|
| 10      | 10        | 63            | 38            |
| 13      | 10        | 73            | 96            |
| 7       | 8         | 100           | 28            |
| 17      | 7         | 119           | 68            |
| 20      | 1         | 121           | 92            |
| 5       | 7         | 42            | 101           |
| 2       | 4         | 6             | 38            |

**Output**

| month | working_percentage |
|-------|--------------------|
| 1     | 0.00               |
| 2     | 0.00               |
| 3     | 25.00              |
| 4     | 0.00               |
| 5     | 0.00               |
| 6     | 20.00              |
| 7     | 20.00              |
| 8     | 20.00              |
| 9     | 0.00               |
| 10    | 0.00               |
| 11    | 33.33              |
| 12    | 16.67              |

---
### Thought Process

1. **Identify the Active Drivers**:
   - Extract the month each driver became active (`join_date`) and determine the months they remain active until the end of 2020.

2. **Calculate Accepted Rides**:
   - Identify the number of unique drivers who accepted rides in each month.

3. **Combine and Calculate Percentages**:
   - For each month, compute the percentage of active drivers who accepted at least one ride.

4. **Edge Cases**:
   - Handle months with zero active drivers by setting the working percentage to `0.00`.

---
### Query
```sql
WITH RECURSIVE Months AS (
    -- Generate months from 1 to 12
    SELECT 1 AS month
    UNION ALL
    SELECT month + 1
    FROM Months
    WHERE month < 12
),

DriverStartMonth AS (
    -- Map drivers to their starting month
    SELECT 
        driver_id, 
        CASE 
            WHEN YEAR(join_date) = 2019 THEN 1  -- Pre-2020 joins start in January
            ELSE MONTH(join_date) 
        END AS start_month
    FROM drivers
    WHERE YEAR(join_date) <= 2020
),

ActiveDriversByMonth AS (
    -- Calculate active drivers by month
    SELECT 
        m.month,
        COUNT(DISTINCT d.driver_id) AS available_drivers
    FROM Months m
    LEFT JOIN DriverStartMonth d ON m.month >= d.start_month
    GROUP BY m.month
),

AcceptedRidesByMonth AS (
    -- Calculate drivers who accepted rides per month
    SELECT 
        MONTH(r.requested_at) AS month,
        COUNT(DISTINCT ar.driver_id) AS accepted_ride_drivers
    FROM rides r
    JOIN acceptedrides ar ON r.ride_id = ar.ride_id
    WHERE YEAR(r.requested_at) = 2020
    GROUP BY MONTH(r.requested_at)
)

-- Final calculation
SELECT 
    m.month,
    COALESCE(ROUND((arbm.accepted_ride_drivers / adb.available_drivers) * 100, 2), 0) AS working_percentage
FROM Months m
LEFT JOIN ActiveDriversByMonth adb ON m.month = adb.month
LEFT JOIN AcceptedRidesByMonth arbm ON m.month = arbm.month
ORDER BY m.month;
