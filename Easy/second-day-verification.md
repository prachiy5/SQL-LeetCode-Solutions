### Second Day Verification

#### Table: emails

```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| email_id    | int      |
| user_id     | int      |
| signup_date | datetime |
+-------------+----------+
```
- `(email_id, user_id)` is the primary key (combination of columns with unique values) for this table.
- Each row of this table contains the email ID, user ID, and signup date.

#### Table: texts

```
+---------------+----------+
| Column Name   | Type     |
+---------------+----------+
| text_id       | int      |
| email_id      | int      |
| signup_action | enum     |
| action_date   | datetime |
+---------------+----------+
```
- `(text_id, email_id)` is the primary key (combination of columns with unique values) for this table.
- `signup_action` is an enum type of `('Verified', 'Not Verified')`.
- Each row of this table contains the text ID, email ID, signup action, and action date.

### Task

Write a solution to find the `user_id` of those who verified their sign-up on the second day.

Return the result table ordered by `user_id` in ascending order.

---

### Example:

**Input:**

emails table:

| email_id | user_id | signup_date         |
|----------|---------|---------------------|
| 125      | 7771    | 2022-06-14 09:30:00|
| 433      | 1052    | 2022-07-09 08:15:00|
| 234      | 7005    | 2022-08-20 10:00:00|

texts table:

| text_id | email_id | signup_action| action_date         |
|---------|----------|--------------|---------------------|
| 1       | 125      | Verified     | 2022-06-15 08:30:00|
| 2       | 433      | Not Verified | 2022-07-10 10:45:00|
| 4       | 234      | Verified     | 2022-08-21 09:30:00|

**Output:**

| user_id |
|---------|
| 7005    |
| 7771    |

---

### Explanation:
- User with `user_id 7005` and `email_id 234` signed up on `2022-08-20 10:00:00` and verified on the second day of signup (`2022-08-21 09:30:00`).
- User with `user_id 7771` and `email_id 125` signed up on `2022-06-14 09:30:00` and verified on the second day of signup (`2022-06-15 08:30:00`).
- User `1052` did not verify their signup on the second day, so they are excluded.

---

### Thought Process:

1. **Understand the Requirement**: Identify users who signed up and verified their account the next day (`second day`).
2. **Key Steps**:
   - Join the `emails` table and `texts` table on `email_id`.
   - Calculate the `second day` of signup by adding one day to `signup_date`.
   - Compare this calculated date with `action_date` for `Verified` actions.
3. **Filter and Order**:
   - Filter rows where `action_date` matches the `second day` and the action is `Verified`.
   - Return the result ordered by `user_id` in ascending order.

---

### Query:

```sql
WITH cte AS (
  SELECT 
    e.email_id,
    e.user_id,
    e.signup_date,
    t.action_date,
    t.signup_action
  FROM emails e
  INNER JOIN texts t
  ON e.email_id = t.email_id
)

SELECT user_id 
FROM cte
WHERE DATE_ADD(DATE(signup_date), INTERVAL 1 DAY) = DATE(action_date)
  AND signup_action = 'Verified'
ORDER BY user_id;
```

---

### Explanation of the Query:

1. **Join Emails and Texts**:
   - Combine the `emails` and `texts` tables using an `INNER JOIN` on the `email_id` column.
2. **Calculate Second Day**:
   - Use `DATE_ADD(DATE(signup_date), INTERVAL 1 DAY)` to calculate the `second day` from `signup_date`.
   - Use `DATE(action_date)` to extract only the date part of `action_date`.
3. **Filter for Verified Actions**:
   - Check that the `action_date` matches the calculated second day.
   - Ensure the `signup_action` is `'Verified'`.
4. **Order Results**:
   - Use `ORDER BY user_id` to sort the result in ascending order.

---

### Why This Query Works:
- The `WITH` clause simplifies the query by organizing the joined table.
- The `DATE_ADD` and `DATE` functions ensure proper comparison of dates.
- Filtering with `signup_action = 'Verified'` ensures only verified users are included.
- Sorting by `user_id` meets the output requirement.

---

### Output:

For the given input, the result table is:

| user_id |
|---------|
| 7005    |
| 7771    |
