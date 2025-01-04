### The Users That Are Eligible for Discount

Table: Purchases

```plaintext
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| user_id     | int      |
| time_stamp  | datetime |
| amount      | int      |
+-------------+----------+
```
- **user_id**: The ID of the user who made the purchase.
- **time_stamp**: The date and time when the purchase was made.
- **amount**: The amount spent in the purchase.

Each row in this table contains information about the purchases made by users, including the time and amount of the purchase.

**Conditions for Discount Eligibility:**
- A user is eligible for a discount if they have made at least one purchase:
  1. Within the inclusive interval `[startDate, endDate]`.
  2. With an amount greater than or equal to `minAmount`.

**Key Notes:**
- Both `startDate` and `endDate` should be considered as the start of the day (e.g., `2022-03-05` is treated as `2022-03-05 00:00:00`).
- Return the IDs of users who meet these conditions, sorted by `user_id` in ascending order.

### Example:

#### Input:
Purchases table:
```plaintext
+---------+---------------------+--------+
| user_id | time_stamp          | amount |
+---------+---------------------+--------+
| 1       | 2022-04-20 09:03:00 | 4416   |
| 2       | 2022-03-19 19:24:02 | 678    |
| 3       | 2022-03-18 12:03:09 | 4523   |
| 3       | 2022-03-30 09:43:42 | 626    |
+---------+---------------------+--------+
```
`startDate = 2022-03-08`, `endDate = 2022-03-20`, `minAmount = 1000`

#### Output:
```plaintext
+---------+
| user_id |
+---------+
| 3       |
+---------+
```

#### Explanation:
- User `1` made a purchase with an amount greater than `1000`, but it was outside the date range.
- User `2` made a purchase within the date range but with an amount less than `1000`.
- User `3` made a purchase within the date range with an amount greater than `1000`, making them eligible.

---

### Thought Process:

1. **Understand the Conditions:**
   - The user must have at least one purchase within the given date range (`startDate` to `endDate`).
   - The amount of the purchase should be at least `minAmount`.

2. **Filter the Data:**
   - Use the `WHERE` clause to filter purchases that fall within the given date range and have an amount greater than or equal to `minAmount`.

3. **Eliminate Duplicates:**
   - Use `DISTINCT` to ensure each eligible user is listed only once.

4. **Sort the Result:**
   - Sort the output by `user_id` in ascending order as required.

---

### Query:

```sql
CREATE PROCEDURE getUserIDs(startDate DATE, endDate DATE, minAmount INT)
BEGIN
    SELECT DISTINCT user_id
    FROM purchases
    WHERE time_stamp BETWEEN TIMESTAMP(startDate) AND TIMESTAMP(endDate)
      AND amount >= minAmount
    ORDER BY user_id;
END;
```

---

### Explanation of the Query:

1. **Filter by Date Range:**
   - `time_stamp BETWEEN TIMESTAMP(startDate) AND TIMESTAMP(endDate)` ensures we only consider purchases made within the given date range.

2. **Filter by Amount:**
   - `amount >= minAmount` ensures only purchases with sufficient amounts are considered.

3. **Select Unique Users:**
   - `DISTINCT user_id` ensures each user appears only once in the result, even if they made multiple qualifying purchases.

4. **Sort the Results:**
   - `ORDER BY user_id` sorts the output in ascending order of user IDs.

---

### Why This Works:
- This query ensures we capture only the user IDs that meet both conditions of the discount eligibility. The use of `DISTINCT` and filtering logic guarantees correctness and completeness.


