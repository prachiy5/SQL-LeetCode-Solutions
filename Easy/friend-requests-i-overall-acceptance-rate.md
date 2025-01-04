# Friend Requests I: overall acceptance

### Problem

#### Table: FriendRequest

| Column Name  | Type |
|--------------|------|
| sender_id    | int  |
| send_to_id   | int  |
| request_date | date |

- This table may contain duplicate rows, meaning it does not have a primary key.
- Each row represents a friend request sent by `sender_id` to `send_to_id` on a specific date.

#### Table: RequestAccepted

| Column Name  | Type |
|--------------|------|
| requester_id | int  |
| accepter_id  | int  |
| accept_date  | date |

- This table may contain duplicate rows, meaning it does not have a primary key.
- Each row represents a friend request accepted by `accepter_id` from `requester_id` on a specific date.

#### Task
Find the overall acceptance rate of friend requests, which is the total number of accepted requests divided by the total number of requests. The result should be rounded to two decimal places. If there are no requests, return `0.00`.

**Notes:**
1. Only count unique requests and unique acceptances.
2. If there are duplicate requests or acceptances, count each pair `(sender_id, send_to_id)` or `(requester_id, accepter_id)` only once.
3. The accepted requests may or may not originate from the `FriendRequest` table.

---

### Example

**Input:**

`FriendRequest` table:

| sender_id | send_to_id | request_date |
|-----------|------------|--------------|
| 1         | 2          | 2016/06/01   |
| 1         | 3          | 2016/06/01   |
| 1         | 4          | 2016/06/01   |
| 2         | 3          | 2016/06/02   |
| 3         | 4          | 2016/06/09   |

`RequestAccepted` table:

| requester_id | accepter_id | accept_date |
|--------------|-------------|-------------|
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
| 3            | 4           | 2016/06/10  |

**Output:**

| accept_rate |
|-------------|
| 0.8         |

**Explanation:**
- There are 5 unique requests in total (`1->2`, `1->3`, `1->4`, `2->3`, `3->4`).
- There are 4 unique accepted requests in total (`1->2`, `1->3`, `2->3`, `3->4`).
- Acceptance rate = 4 / 5 = 0.80.

---

### Thought Process

To calculate the acceptance rate:

1. **Identify Unique Requests:**
   - Use `DISTINCT` on `(sender_id, send_to_id)` from the `FriendRequest` table to count unique requests.

2. **Identify Unique Acceptances:**
   - Use `DISTINCT` on `(requester_id, accepter_id)` from the `RequestAccepted` table to count unique acceptances.

3. **Calculate Acceptance Rate:**
   - Divide the count of unique acceptances by the count of unique requests.
   - If there are no requests, return `0.00` to handle the edge case.

4. **Round the Result:**
   - Use the `ROUND()` function to round the result to two decimal places.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT IFNULL(ROUND(
  (SELECT COUNT(DISTINCT requester_id, accepter_id) FROM RequestAccepted) /
  (SELECT COUNT(DISTINCT sender_id, send_to_id) FROM FriendRequest),
  2), 0.00) AS accept_rate;
```

---

### Solution Explanation

1. **COUNT(DISTINCT):**
   - Ensures only unique pairs are counted from both tables.
   - `(requester_id, accepter_id)` in `RequestAccepted` for unique acceptances.
   - `(sender_id, send_to_id)` in `FriendRequest` for unique requests.

2. **IFNULL:**
   - Handles cases where there are no requests, returning `0.00` instead of `NULL`.

3. **ROUND:**
   - Rounds the division result to two decimal places for the final acceptance rate.

---

### Key Takeaways

- **DISTINCT:** Essential for counting unique values.
- **IFNULL Function:** Ensures a default value when there is no data.
- **Edge Cases:** Always account for scenarios where the divisor might be `0`.


