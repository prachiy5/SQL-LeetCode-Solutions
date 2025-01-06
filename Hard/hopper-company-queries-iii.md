### Hopper Company Queries III

**Table: Drivers**

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| join_date   | date    |
+-------------+---------+
```
- `driver_id` is the column with unique values for this table.
- Each row of this table contains the driver's ID and the date they joined the Hopper company.

**Table: Rides**

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |
+--------------+---------+
```
- `ride_id` is the column with unique values for this table.
- Each row of this table contains the ID of a ride, the user's ID that requested it, and the day they requested it.
- There may be some ride requests in this table that were not accepted.

**Table: AcceptedRides**

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ride_id       | int     |
| driver_id     | int     |
| ride_distance | int     |
| ride_duration | int     |
+---------------+---------+
```
- `ride_id` is the column with unique values for this table.
- Each row of this table contains some information about an accepted ride.
- It is guaranteed that each accepted ride exists in the Rides table.

**Task:**
Write a solution to compute the `average_ride_distance` and `average_ride_duration` of every 3-month window starting from January - March 2020 to October - December 2020. Round `average_ride_distance` and `average_ride_duration` to the nearest two decimal places.

- The `average_ride_distance` is calculated by summing up the total `ride_distance` values from the three months and dividing it by 3.
- The `average_ride_duration` is calculated similarly.

Return the result table ordered by `month` in ascending order, where `month` is the starting month's number (January is 1, February is 2, etc.).

---

### Example Input:

**Drivers table:**

```
+-----------+------------+
| driver_id | join_date  |
+-----------+------------+
| 10        | 2019-12-10 |
| 8         | 2020-1-13  |
| 5         | 2020-2-16  |
| 7         | 2020-3-8   |
| 4         | 2020-5-17  |
| 1         | 2020-10-24 |
| 6         | 2021-1-5   |
+-----------+------------+
```

**Rides table:**

```
+---------+---------+--------------+
| ride_id | user_id | requested_at |
+---------+---------+--------------+
| 6       | 75      | 2019-12-9    |
| 1       | 54      | 2020-2-9     |
| 10      | 63      | 2020-3-4     |
| 19      | 39      | 2020-4-6     |
| 3       | 41      | 2020-6-3     |
| 13      | 52      | 2020-6-22    |
| 7       | 69      | 2020-7-16    |
| 17      | 70      | 2020-8-25    |
| 20      | 81      | 2020-11-2    |
| 5       | 57      | 2020-11-9    |
| 2       | 42      | 2020-12-9    |
| 11      | 68      | 2021-1-11    |
| 15      | 32      | 2021-1-17    |
| 12      | 11      | 2021-1-19    |
| 14      | 18      | 2021-1-27    |
+---------+---------+--------------+
```

**AcceptedRides table:**

```
+---------+-----------+---------------+---------------+
| ride_id | driver_id | ride_distance | ride_duration |
+---------+-----------+---------------+---------------+
| 10      | 10        | 63            | 38            |
| 13      | 10        | 73            | 96            |
| 7       | 8         | 100           | 28            |
| 17      | 7         | 119           | 68            |
| 20      | 1         | 121           | 92            |
| 5       | 7         | 42            | 101           |
| 2       | 4         | 6             | 38            |
| 11      | 8         | 37            | 43            |
| 15      | 8         | 108           | 82            |
| 12      | 8         | 38            | 34            |
| 14      | 1         | 90            | 74            |
+---------+-----------+---------------+---------------+
```

**Output:**

```
+-------+-----------------------+-----------------------+
| month | average_ride_distance | average_ride_duration |
+-------+-----------------------+-----------------------+
| 1     | 21.00                 | 12.67                |
| 2     | 21.00                 | 12.67                |
| 3     | 21.00                 | 12.67                |
| 4     | 24.33                 | 32.00                |
| 5     | 57.67                 | 41.33                |
| 6     | 97.33                 | 64.00                |
| 7     | 73.00                 | 32.00                |
| 8     | 39.67                 | 22.67                |
| 9     | 54.33                 | 64.33                |
| 10    | 56.33                 | 77.00                |
+-------+-----------------------+-----------------------+
```

---

### Thought Process

1. **Understand the problem:**
   - Calculate the average ride distance and duration over every rolling 3-month window in 2020.
   - For each month, consider the data for that month and the next two months.

2. **Break the problem into steps:**
   - Generate a list of months (1 to 12) for 2020.
   - Aggregate ride distances and durations by month.
   - Ensure all months are represented, filling missing months with zeros.
   - Compute the rolling 3-month averages for each month.

3. **Plan the SQL query:**
   - Use recursive CTE to generate the list of months.
   - Aggregate data from `Rides` and `AcceptedRides` for monthly totals.
   - Use `IFNULL` to handle missing months.
   - Use a `WINDOW FUNCTION` to compute rolling averages for 3 months.

---

### SQL Query

```sql
WITH RECURSIVE month AS (
    -- Generate months 1 to 12
    SELECT 1 AS month
    UNION
    SELECT month + 1 AS month
    FROM month
    WHERE month < 12
),

month_rides AS (
    -- Aggregate the total ride distance and duration per month for 2020
    SELECT   
        MONTH(r.requested_at) AS month,
        SUM(a.ride_distance) AS ride_distance,
        SUM(a.ride_duration) AS ride_duration
    FROM Rides r 
    JOIN AcceptedRides a ON a.ride_id = r.ride_id
    WHERE YEAR(r.requested_at) = '2020'
    GROUP BY MONTH(r.requested_at)
),

every_month_rides AS (
    -- Ensure each month has ride distance and duration values, filling in zeroes for missing months
    SELECT 
        m.month, 
        IFNULL(mr.ride_distance, 0) AS ride_distance, 
        IFNULL(mr.ride_duration, 0) AS ride_duration
    FROM month m 
    LEFT JOIN month_rides mr ON mr.month = m.month
)

-- Calculate the rolling 3-month average directly
SELECT 
    month,
    ROUND(AVG(ride_distance) OVER (ORDER BY month ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING), 2) AS average_ride_distance,
    ROUND(AVG(ride_duration) OVER (ORDER BY month ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING), 2) AS average_ride_duration
FROM every_month_rides
ORDER BY month
limit 10

