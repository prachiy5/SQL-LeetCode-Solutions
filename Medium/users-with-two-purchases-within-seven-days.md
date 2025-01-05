### Users With Two Purchases Within Seven Days

Table: Purchases
```
+---------------+------+
| Column Name   | Type |
+---------------+------+
| purchase_id   | int  |
| user_id       | int  |
| purchase_date | date |
+---------------+------+
```
purchase_id contains unique values.
This table contains logs of the dates that users purchased from a certain retailer.

Write a solution to report the IDs of the users that made any two purchases at most 7 days apart.

Return the result table ordered by user_id.

The result format is in the following example.

### Example

Input: 
Purchases table:
```
+-------------+---------+---------------+
| purchase_id | user_id | purchase_date |
+-------------+---------+---------------+
| 4           | 2       | 2022-03-13    |
| 1           | 5       | 2022-02-11    |
| 3           | 7       | 2022-06-19    |
| 6           | 2       | 2022-03-20    |
| 5           | 7       | 2022-06-19    |
| 2           | 2       | 2022-06-08    |
+-------------+---------+---------------+
```
Output: 
```
+---------+
| user_id |
+---------+
| 2       |
| 7       |
+---------+
```
### Explanation:

- User 2 had two purchases on 2022-03-13 and 2022-03-20. Since the second purchase is within 7 days of the first purchase, we add their ID.
- User 5 had only 1 purchase.
- User 7 had two purchases on the same day so we add their ID.

---

### Thought Process

To solve this, we need to:
1. Identify all users who have made at least two purchases within a 7-day interval.
2. Use a window function (`LAG`) to calculate the previous purchase date for each user.
3. Filter the results to include only those where the difference between the current and previous purchase dates is 7 days or fewer.
4. Use `DISTINCT` to ensure each user ID is reported only once.
5. Order the result by `user_id`.

---

### Query

```sql
WITH cte AS (
    SELECT 
        purchase_id,
        user_id,
        purchase_date,
        LAG(purchase_date) OVER (PARTITION BY user_id ORDER BY purchase_date) AS prev_purchase_date
    FROM 
        purchases
)

SELECT DISTINCT 
    user_id 
FROM 
    cte
WHERE 
    DATEDIFF(purchase_date, prev_purchase_date) <= 7
ORDER BY 
    user_id;
