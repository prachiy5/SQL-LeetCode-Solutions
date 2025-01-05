# Trips and Users

You are given two tables:

### Trips Table
| Column Name | Type     |
|-------------|----------|
| id          | int      |
| client_id   | int      |
| driver_id   | int      |
| city_id     | int      |
| status      | enum     |
| request_at  | varchar  |

- `id` is the primary key (column with unique values) for this table.
- The table holds all taxi trips. Each trip has a unique `id`, while `client_id` and `driver_id` are foreign keys to the `users_id` in the `Users` table.
- `status` is an ENUM type of (`'completed'`, `'cancelled_by_driver'`, `'cancelled_by_client'`).

### Users Table
| Column Name | Type     |
|-------------|----------|
| users_id    | int      |
| banned      | enum     |
| role        | enum     |

- `users_id` is the primary key (column with unique values) for this table.
- The table holds all users. Each user has a unique `users_id`, and `role` is an ENUM type of (`'client'`, `'driver'`, `'partner'`).
- `banned` is an ENUM type of (`'Yes'`, `'No'`).

### Task
The **cancellation rate** is computed by dividing the number of canceled (by client or driver) requests with unbanned users by the total number of requests with unbanned users on that day.

Write a solution to find the cancellation rate of requests with unbanned users (both client and driver must not be banned) for each day between `"2013-10-01"` and `"2013-10-03"`. Round the cancellation rate to two decimal points.

Return the result table in any order.

### Input Example
#### Trips Table:
| id | client_id | driver_id | city_id | status              | request_at |
|----|-----------|-----------|---------|---------------------|------------|
| 1  | 1         | 10        | 1       | completed           | 2013-10-01 |
| 2  | 2         | 11        | 1       | cancelled_by_driver | 2013-10-01 |
| 3  | 3         | 12        | 6       | completed           | 2013-10-01 |
| 4  | 4         | 13        | 6       | cancelled_by_client | 2013-10-01 |
| 5  | 1         | 10        | 1       | completed           | 2013-10-02 |
| 6  | 2         | 11        | 6       | completed           | 2013-10-02 |
| 7  | 3         | 12        | 6       | completed           | 2013-10-02 |
| 8  | 2         | 12        | 12      | completed           | 2013-10-03 |
| 9  | 3         | 10        | 12      | completed           | 2013-10-03 |
| 10 | 4         | 13        | 12      | cancelled_by_driver | 2013-10-03 |

#### Users Table:
| users_id | banned | role   |
|----------|--------|--------|
| 1        | No     | client |
| 2        | Yes    | client |
| 3        | No     | client |
| 4        | No     | client |
| 10       | No     | driver |
| 11       | No     | driver |
| 12       | No     | driver |
| 13       | No     | driver |

### Output Example
| Day        | Cancellation Rate |
|------------|-------------------|
| 2013-10-01 | 0.33              |
| 2013-10-02 | 0.00              |
| 2013-10-03 | 0.50              |

### Explanation

- **2013-10-01**:
  - Total requests: 4.
  - Excluding banned users: Requests with `id=2` are excluded (banned client `users_id=2`).
  - Unbanned requests: 3.
  - Canceled requests: 1 (request `id=4`).
  - Cancellation Rate = `(1 / 3) = 0.33`.

- **2013-10-02**:
  - Total requests: 3.
  - Excluding banned users: Requests with `id=6` are excluded (banned client `users_id=2`).
  - Unbanned requests: 2.
  - Canceled requests: 0.
  - Cancellation Rate = `(0 / 2) = 0.00`.

- **2013-10-03**:
  - Total requests: 3.
  - Excluding banned users: Requests with `id=8` are excluded (banned client `users_id=2`).
  - Unbanned requests: 2.
  - Canceled requests: 1 (request `id=10`).
  - Cancellation Rate = `(1 / 2) = 0.50`.

### Thought Process

1. **Filter Trips with Unbanned Users**:
   - Join the `Trips` table with the `Users` table twice (once for `client_id` and once for `driver_id`).
   - Include only trips where both the client and driver are not banned.

2. **Filter Date Range**:
   - Include only trips between `"2013-10-01"` and `"2013-10-03"`.

3. **Count Canceled Trips**:
   - Identify trips with `status` as `'cancelled_by_driver'` or `'cancelled_by_client'`.
   - Group by `date_of_trip` to calculate the number of canceled trips.

4. **Count Total Trips**:
   - Count all unbanned trips for each day.

5. **Calculate Cancellation Rate**:
   - Divide the number of canceled trips by the total number of trips for each day.
   - Round the result to two decimal points.

### Query
```sql
WITH cte1 AS (
    SELECT 
        t.id, 
        t.client_id, 
        t.driver_id, 
        t.city_id, 
        t.status, 
        t.request_at AS date_of_trip
    FROM 
        trips t
    JOIN 
        users uc 
        ON t.client_id = uc.users_id AND uc.banned = 'No'
    JOIN 
        users ud 
        ON t.driver_id = ud.users_id AND ud.banned = 'No'
    WHERE 
        t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
),

cancelled_trips AS (
    SELECT 
        COUNT(*) AS cancelled_trip, 
        date_of_trip 
    FROM 
        cte1
    WHERE 
        status = 'cancelled_by_driver'
        OR status = 'cancelled_by_client'
    GROUP BY 
        date_of_trip
),

total_trips AS (
    SELECT 
        COUNT(*) AS total_request, 
        date_of_trip
    FROM 
        cte1
    GROUP BY 
        date_of_trip
)

SELECT 
    t.date_of_trip AS `day`, 
    ROUND(IFNULL(c.cancelled_trip, 0) / t.total_request, 2) AS `Cancellation Rate`
FROM 
    total_trips t
LEFT JOIN 
    cancelled_trips c
ON 
    t.date_of_trip = c.date_of_trip;
