### The Number of Passengers in Each Bus I

**Table: Buses**
```
+--------------+------+
| Column Name  | Type |
+--------------+------+
| bus_id       | int  |
| arrival_time | int  |
+--------------+------+
```
bus_id is the column with unique values for this table.
Each row of this table contains information about the arrival time of a bus at the LeetCode station.
No two buses will arrive at the same time.

**Table: Passengers**
```
+--------------+------+
| Column Name  | Type |
+--------------+------+
| passenger_id | int  |
| arrival_time | int  |
+--------------+------+
```
passenger_id is the column with unique values for this table.
Each row of this table contains information about the arrival time of a passenger at the LeetCode station.

Buses and passengers arrive at the LeetCode station. If a bus arrives at the station at time `tbus` and a passenger arrived at time `tpassenger` where `tpassenger <= tbus` and the passenger did not catch any bus, the passenger will use that bus.

Write a solution to report the number of users that used each bus.

Return the result table ordered by `bus_id` in ascending order.

**Example 1:**

Input: 

**Buses table:**
```
+--------+--------------+
| bus_id | arrival_time |
+--------+--------------+
| 1      | 2            |
| 2      | 4            |
| 3      | 7            |
+--------+--------------+
```
**Passengers table:**
```
+--------------+--------------+
| passenger_id | arrival_time |
+--------------+--------------+
| 11           | 1            |
| 12           | 5            |
| 13           | 6            |
| 14           | 7            |
+--------------+--------------+
```
Output: 
```
+--------+----------------+
| bus_id | passengers_cnt |
+--------+----------------+
| 1      | 1              |
| 2      | 0              |
| 3      | 3              |
+--------+----------------+
```
**Explanation:**

- Passenger 11 arrives at time 1.
- Bus 1 arrives at time 2 and collects passenger 11.
- Bus 2 arrives at time 4 and does not collect any passengers.
- Passenger 12 arrives at time 5.
- Passenger 13 arrives at time 6.
- Passenger 14 arrives at time 7.
- Bus 3 arrives at time 7 and collects passengers 12, 13, and 14.

---

### Thought Process

1. **Identify Relevant Passengers for Each Bus:** 
   - For each bus, identify passengers who arrived before or at the bus's arrival time.
   - Ensure passengers are not counted by a previous bus by considering the arrival times of preceding buses.

2. **Handle Previous Bus Logic:**
   - Use a `LAG` window function to determine the arrival time of the previous bus for each bus.

3. **Count Passengers for Each Bus:**
   - Use a `CASE` statement to count passengers who meet the conditions for each bus.

4. **Group and Order Results:**
   - Group results by `bus_id` and order by `bus_id` in ascending order.

---

### Query

```sql
WITH cte AS (
    SELECT *,
           LAG(arrival_time, 1) OVER (ORDER BY arrival_time) AS prev_bus_time
    FROM buses
)

SELECT 
    c.bus_id, 
    SUM(CASE 
            WHEN p.arrival_time <= c.arrival_time 
                 AND p.arrival_time > IFNULL(c.prev_bus_time, 0) 
            THEN 1 
            ELSE 0 
        END) AS passengers_cnt 
FROM cte c
LEFT JOIN passengers p
    ON p.arrival_time <= c.arrival_time
GROUP BY c.bus_id
ORDER BY c.bus_id;
