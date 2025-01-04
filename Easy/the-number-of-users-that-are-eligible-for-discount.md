### The Number of Users That Are Eligible for Discount

Table: Purchases

```text
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| user_id     | int      |
| time_stamp  | datetime |
| amount      | int      |
+-------------+----------+
(user_id, time_stamp) is the primary key (combination of columns with unique values) for this table.
Each row contains information about the purchase time and the amount paid for the user with ID user_id.
```

A user is eligible for a discount if they had a purchase in the inclusive interval of time `[startDate, endDate]` with at least `minAmount` amount. To convert the dates to times, both dates should be considered as the start of the day (i.e., `endDate = 2022-03-05` should be considered as the time `2022-03-05 00:00:00`).

Write a solution to report the number of users that are eligible for a discount.

### Input:

Example table:

```text
+---------+---------------------+--------+
| user_id | time_stamp          | amount |
+---------+---------------------+--------+
| 1       | 2022-04-20 09:03:00 | 4416   |
| 2       | 2022-03-19 19:24:02 | 678    |
| 3       | 2022-03-18 12:03:09 | 4523   |
| 3       | 2022-03-30 09:43:42 | 626    |
+---------+---------------------+--------+
```

With the following parameters:

- `startDate = 2022-03-08`
- `endDate = 2022-03-20`
- `minAmount = 1000`

### Output:

```text
+----------+
| user_cnt |
+----------+
| 1        |
+----------+
```

Explanation:
Out of the three users, only User 3 is eligible for a discount:
- User 1 had one purchase with at least `minAmount` amount, but not within the time interval.
- User 2 had one purchase within the time interval, but with less than `minAmount` amount.
- User 3 is the only user who had a purchase that satisfies both conditions.

---

### Thought Process:

1. **Understand the requirements:**
   - We need to count the number of unique users (`user_id`) who meet two conditions:
     - Their purchase was made within the specified date range (`startDate` to `endDate`).
     - The amount of the purchase was greater than or equal to `minAmount`.

2. **Approach:**
   - Use the `BETWEEN` clause to filter purchases within the specified date range.
   - Add another condition in the `WHERE` clause to check if the `amount` is greater than or equal to `minAmount`.
   - Use `DISTINCT` to ensure we count each qualifying user only once.
   - Use `COUNT` to calculate the total number of such users.

3. **Key points to note:**
   - The dates need to be converted to start of the day (`00:00:00`) using `TIMESTAMP` for accurate comparisons.
   - Purchases outside the specified date range or below the minimum amount do not qualify.

---

### Query:

```sql
CREATE FUNCTION getUserIDs(startDate DATE, endDate DATE, minAmount INT) RETURNS INT
BEGIN
  RETURN (
    SELECT COUNT(DISTINCT user_id)
    FROM Purchases
    WHERE time_stamp BETWEEN TIMESTAMP(startDate) AND TIMESTAMP(endDate)
      AND amount >= minAmount
  );
END;
```

---

### Explanation of the Query:

1. **Filter by date range:**
   - `time_stamp BETWEEN TIMESTAMP(startDate) AND TIMESTAMP(endDate)` ensures only purchases within the specified range are included.

2. **Filter by minimum amount:**
   - `amount >= minAmount` ensures only purchases meeting or exceeding the minimum amount qualify.

3. **Count distinct users:**
   - `COUNT(DISTINCT user_id)` counts unique users who satisfy the conditions.

4. **Wrap the logic in a function:**
   - The `CREATE FUNCTION` statement creates a reusable function to calculate the eligible user count dynamically based on input parameters (`startDate`, `endDate`, `minAmount`).

---

### Output:

For the given input data:

- User 3 qualifies with a purchase on `2022-03-18` for `4523`.
- Total eligible users = `1`.

Result table:

```text
+----------+
| user_cnt |
+----------+
| 1        |
+----------+
