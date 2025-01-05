
### Number of Times a Driver Was a Passenger

Table: Rides

```
+--------------+------+
| Column Name  | Type |
+--------------+------+
| ride_id      | int  |
| driver_id    | int  |
| passenger_id | int  |
+--------------+------+
ride_id is the column with unique values for this table.
Each row of this table contains the ID of the driver and the ID of the passenger that rode in ride_id.
Note that driver_id != passenger_id.
```

Write a solution to report the ID of each driver and the number of times they were a passenger.

Return the result table in any order.

The result format is in the following example.

### Example

#### Input:

Rides table:

```
+---------+-----------+--------------+
| ride_id | driver_id | passenger_id |
+---------+-----------+--------------+
| 1       | 7         | 1            |
| 2       | 7         | 2            |
| 3       | 11        | 1            |
| 4       | 11        | 7            |
| 5       | 11        | 7            |
| 6       | 11        | 3            |
+---------+-----------+--------------+
```

#### Output:

```
+-----------+-----+
| driver_id | cnt |
+-----------+-----+
| 7         | 2   |
| 11        | 0   |
+-----------+-----+
```

### Explanation:

- There are two drivers in all the given rides: 7 and 11.
- The driver with ID = 7 was a passenger two times.
- The driver with ID = 11 was never a passenger.

---
### Thought Process

1. Identify all distinct driver IDs from the `Rides` table.
2. For each driver, count how many times they appear as a `passenger_id` in the `Rides` table.
3. Handle cases where a driver was never a passenger by ensuring the count is 0 in such scenarios.
4. Use a LEFT JOIN to link drivers with their passenger counts and ensure drivers who were never passengers are included.
5. Group the data by driver ID and count occurrences of them being passengers.
6. Return the driver IDs alongside their respective counts.

---
### SQL Query

```sql
WITH cte AS (
    SELECT DISTINCT driver_id 
    FROM rides
)

SELECT 
    cte.driver_id, 
    IFNULL(COUNT(r.passenger_id), 0) AS cnt
FROM 
    cte 
LEFT JOIN 
    rides r
ON 
    cte.driver_id = r.passenger_id
GROUP BY 
    cte.driver_id;
```

---
### Explanation of the Query

1. **Common Table Expression (CTE)**:
   - Extracts all distinct `driver_id` values from the `Rides` table.

2. **LEFT JOIN**:
   - Links the distinct drivers (`cte`) to the `Rides` table on the condition that the driver ID matches the `passenger_id`.

3. **COUNT and IFNULL**:
   - Counts the occurrences of each driver as a passenger.
   - Uses `IFNULL` to ensure that drivers with no matches in the `passenger_id` column are assigned a count of 0.

4. **GROUP BY**:
   - Groups the results by `driver_id` to calculate the passenger count for each driver.

5. **SELECT Statement**:
   - Returns the `driver_id` alongside the calculated `cnt`.

---
### Output

For the provided input, the output would be:

```
+-----------+-----+
| driver_id | cnt |
+-----------+-----+
| 7         | 2   |
| 11        | 0   |
+-----------+-----+
