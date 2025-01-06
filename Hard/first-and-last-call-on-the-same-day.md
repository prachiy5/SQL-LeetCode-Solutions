### First and Last Call On the Same Day

Table: Calls
```
+--------------+----------+
| Column Name  | Type     |
+--------------+----------+
| caller_id    | int      |
| recipient_id | int      |
| call_time    | datetime |
+--------------+----------+
```
(caller_id, recipient_id, call_time) is the primary key (combination of columns with unique values) for this table.
Each row contains information about the time of a phone call between caller_id and recipient_id.
 
Write a solution to report the IDs of the users whose first and last calls on any day were with the same person. Calls are counted regardless of being the caller or the recipient.

Return the result table in any order.

### Example

Input: 
Calls table:
```
+-----------+--------------+---------------------+
| caller_id | recipient_id | call_time           |
+-----------+--------------+---------------------+
| 8         | 4            | 2021-08-24 17:46:07 |
| 4         | 8            | 2021-08-24 19:57:13 |
| 5         | 1            | 2021-08-11 05:28:44 |
| 8         | 3            | 2021-08-17 04:04:15 |
| 11        | 3            | 2021-08-17 13:07:00 |
| 8         | 11           | 2021-08-17 22:22:22 |
+-----------+--------------+---------------------+
```
Output: 
```
+---------+
| user_id |
+---------+
| 1       |
| 4       |
| 5       |
| 8       |
+---------+
```
### Explanation

- On 2021-08-24, the first and last call of this day for user 8 was with user 4. User 8 should be included in the answer.
- Similarly, user 4 on 2021-08-24 had their first and last call with user 8. User 4 should be included in the answer.
- On 2021-08-11, user 1 and 5 had a call. This call was the only call for both of them on this day. Since this call is the first and last call of the day for both of them, they should both be included in the answer.

---

### Thought Process

1. **Combine Caller and Recipient Data**: To account for both caller and recipient perspectives, normalize the table by unioning rows where the caller and recipient roles are swapped.
   
2. **Rank Calls by Time**: Use `DENSE_RANK()` to identify the first and last calls for each user on each day.

3. **Filter for Matching First and Last Calls**: For each user and day, check if the first and last calls involve the same other user.

4. **Group and Return Unique Users**: Return distinct user IDs where the first and last calls on a day are with the same person.

---

### Query
```sql
WITH cte AS (
    SELECT 
        caller_id AS from_call, 
        recipient_id AS to_call,
        call_time 
    FROM calls
    UNION 
    SELECT 
        recipient_id AS from_call, 
        caller_id AS to_call,
        call_time 
    FROM calls
    ORDER BY call_time
),

cte2 AS (
    SELECT 
        *, 
        DATE(call_time) AS day,
        DENSE_RANK() OVER (PARTITION BY from_call, DATE(call_time) ORDER BY call_time) AS first,
        DENSE_RANK() OVER (PARTITION BY from_call, DATE(call_time) ORDER BY call_time DESC) AS last
    FROM cte
)

SELECT DISTINCT from_call AS user_id 
FROM cte2
WHERE first = 1 OR last = 1
GROUP BY from_call, day
HAVING COUNT(DISTINCT to_call) = 1;
