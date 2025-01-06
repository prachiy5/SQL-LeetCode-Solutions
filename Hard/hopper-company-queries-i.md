### Hopper Company Queries I

**Tables**:

**Drivers**
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| join_date   | date    |
+-------------+---------+
```
- `driver_id` is the primary key (column with unique values) for this table.
- Each row of this table contains the driver's ID and the date they joined the Hopper company.

**Rides**
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |
+--------------+---------+
```
- `ride_id` is the primary key (column with unique values) for this table.
- Each row of this table contains the ID of a ride, the user's ID that requested it, and the day they requested it.
- There may be some ride requests in this table that were not accepted.

**AcceptedRides**
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
- `ride_id` is the primary key (column with unique values) for this table.
- Each row is a record of an accepted ride.
- It is guaranteed that each accepted ride exists in the Rides table.

---

Write a solution to report the following statistics for each month of 2020:

- The number of drivers currently with the Hopper company by the end of the month (`active_drivers`).
- The number of accepted rides in that month (`accepted_rides`).

Return the result table ordered by month in ascending order, where `month` is the month's number (January is 1, February is 2, etc.).

---

**Example Input**:

**Drivers**
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
**Rides**
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
**AcceptedRides**
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
---

**Output**:
```
+-------+----------------+----------------+
| month | active_drivers | accepted_rides |
+-------+----------------+----------------+
| 1     | 2              | 0              |
| 2     | 3              | 0              |
| 3     | 4              | 1              |
| 4     | 4              | 0              |
| 5     | 5              | 0              |
| 6     | 5              | 1              |
| 7     | 5              | 1              |
| 8     | 5              | 1              |
| 9     | 5              | 0              |
| 10    | 6              | 0              |
| 11    | 6              | 2              |
| 12    | 6              | 1              |
+-------+----------------+----------------+
```
---

### Thought Process

1. **Generate All Months of 2020**:
   - Use a recursive CTE to generate a list of months from January (1) to December (12).

2. **Identify Active Drivers**:
   - Use the `Drivers` table to determine drivers who joined by the end of each month.
   - Filter by the join date to ensure drivers who joined in 2021 or later are excluded.

3. **Count Accepted Rides per Month**:
   - Join the `Rides` and `AcceptedRides` tables to find rides accepted in each month of 2020.
   - Group by the ride month to count the total accepted rides.

4. **Combine Data**:
   - Use a `LEFT JOIN` to merge the active driver counts and accepted ride counts for each month.

5. **Output**:
   - Return the result ordered by month in ascending order.

---

### SQL Query

```sql
WITH RECURSIVE month_2020 AS (
    SELECT 1 AS month
    UNION ALL
    SELECT month + 1 FROM month_2020
    WHERE month < 12
),

active_drivers AS (
    SELECT driver_id,
           CASE WHEN YEAR(join_date) <= 2019 THEN 1 ELSE MONTH(join_date) END AS month
    FROM Drivers
    WHERE join_date <= '2020-12-31'
),

accepted_rides AS (
    SELECT a.ride_id, MONTH(requested_at) AS month
    FROM Rides r
    JOIN AcceptedRides a ON r.ride_id = a.ride_id
    WHERE requested_at BETWEEN '2020-01-01' AND '2020-12-31'
)

SELECT 
    m.month,
    COUNT(DISTINCT d.driver_id) AS active_drivers,
    COUNT(DISTINCT a.ride_id) AS accepted_rides
FROM 
    month_2020 m
LEFT JOIN active_drivers d ON m.month >= d.month
LEFT JOIN accepted_rides a ON m.month = a.month
GROUP BY m.month
ORDER BY m.month;
