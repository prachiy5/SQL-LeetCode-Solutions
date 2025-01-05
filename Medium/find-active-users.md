### Find Active Users

We are given a table:

#### Table: Users
| Column Name | Type     |
|-------------|----------|
| user_id     | int      |
| item        | varchar  |
| created_at  | datetime |
| amount      | int      |

- This table may contain duplicate records.
- Each row includes:
  - `user_id`: The ID of the user.
  - `item`: The purchased item.
  - `created_at`: The date and time of the purchase.
  - `amount`: The amount spent.

#### Task
Write a query to identify **active users**. An active user is defined as a user who made a second purchase within 7 days of any of their previous purchases.

For example, if a user made a purchase on May 31, 2023, then any purchase between May 31, 2023, and June 7, 2023 (inclusive) would qualify as a second purchase "within 7 days."

The output should be a list of `user_id` values representing active users. The result can be returned in any order.

---

### Example Input/Output

##### Input:
**Users table:**
| user_id | item              | created_at | amount |
|---------|-------------------|------------|--------|
| 5       | Smart Crock Pot   | 2021-09-18 | 698882 |
| 6       | Smart Lock        | 2021-09-14 | 11487  |
| 6       | Smart Thermostat  | 2021-09-10 | 674762 |
| 8       | Smart Light Strip | 2021-09-29 | 630773 |
| 4       | Smart Cat Feeder  | 2021-09-02 | 693545 |
| 4       | Smart Bed         | 2021-09-13 | 170249 |

##### Output:
| user_id |
|---------|
| 6       |

##### Explanation:
- **User 5:** Only one transaction, so not an active user.
- **User 6:** Two transactions:
  - First transaction: 2021-09-10.
  - Second transaction: 2021-09-14.
  - The time difference is \( 2021-09-14 - 2021-09-10 = 4 \) days, which is \( \leq 7 \), so user 6 is active.
- **User 8:** Only one transaction, so not an active user.
- **User 4:** Two transactions:
  - First transaction: 2021-09-02.
  - Second transaction: 2021-09-13.
  - The time difference is \( 2021-09-13 - 2021-09-02 = 11 \) days, which is \( > 7 \), so user 4 is not active.

---

### Thought Process
1. Use a Common Table Expression (CTE) to assign a row number (`rn`) to each transaction for each user, ordered by `created_at`.
2. Join the CTE to itself to compare pairs of transactions for the same user (`c1` and `c2`).
3. Ensure that the second transaction (`c2`) comes after the first (`c1`) by checking `c1.rn < c2.rn`.
4. Calculate the difference in days between `c2.created_at` and `c1.created_at` using the `DATEDIFF()` function.
5. Filter pairs where the difference is \( \leq 7 \) days.
6. Return the distinct `user_id` values of such users.

---

### Query
```sql
WITH cte AS (
    SELECT user_id, created_at,
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at) AS rn
    FROM users
)

SELECT DISTINCT c1.user_id
FROM cte c1
INNER JOIN cte c2
ON c1.user_id = c2.user_id
   AND c1.rn < c2.rn
   AND DATEDIFF(c2.created_at, c1.created_at) <= 7;
```

---

### Key Takeaways
- **Window Functions:** `ROW_NUMBER()` is used to create an ordered sequence of transactions for each user.
- **Self-Join:** Joining the CTE to itself allows us to compare each transaction with all later transactions for the same user.
- **Date Difference:** `DATEDIFF()` is used to calculate the number of days between two dates.
- **Distinct Results:** Using `DISTINCT` ensures that each active user is listed only once.

---

### Related Topics
- SQL Window Functions
- Self-Joins
- Date and Time Calculations in SQL
- Filtering and Aggregation
