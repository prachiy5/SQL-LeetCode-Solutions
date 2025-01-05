### Loan Types

#### Table: Loans

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| loan_id     | int     |
| user_id     | int     |
| loan_type   | varchar |
+-------------+---------+
```

- `loan_id` is a column of unique values for this table.
- This table contains `loan_id`, `user_id`, and `loan_type`.

Write a solution to find all distinct `user_id` values that have at least one `Refinance` loan type and at least one `Mortgage` loan type.

Return the result table ordered by `user_id` in ascending order.

### Example 1:

#### Input:

Loans table:

```
+---------+---------+-----------+
| loan_id | user_id | loan_type |
+---------+---------+-----------+
| 683     | 101     | Mortgage  |
| 218     | 101     | AutoLoan  |
| 802     | 101     | Inschool  |
| 593     | 102     | Mortgage  |
| 138     | 102     | Refinance |
| 294     | 102     | Inschool  |
| 308     | 103     | Refinance |
| 389     | 104     | Mortgage  |
+---------+---------+-----------+
```

#### Output:

```
+---------+
| user_id |
+---------+
| 102     |
+---------+
```

#### Explanation:
- `user_id 101` has three loan types, one of which is a `Mortgage`. However, this user does not have any loan type categorized as `Refinance`, so `user_id 101` won't be considered.
- `user_id 102` possesses three loan types: one for `Mortgage` and one for `Refinance`. Hence, `user_id 102` will be included in the result.
- `user_id 103` has a loan type of `Refinance` but lacks a `Mortgage` loan type, so `user_id 103` won't be considered.
- `user_id 104` has a `Mortgage` loan type but doesn't have a `Refinance` loan type, thus, `user_id 104` won't be considered.
- The output table is ordered by `user_id` in ascending order.

---

### Thought Process

1. **Understand the Problem:**
   - Identify users (`user_id`) who have both `Mortgage` and `Refinance` loan types.
   - Exclude users who have only one of these loan types.

2. **Approach:**
   - Filter the rows to include only those with `loan_type` as `Mortgage` or `Refinance`.
   - Group by `user_id` to analyze each user's loan types.
   - Use `HAVING` to check that each user has at least two distinct loan types (`Mortgage` and `Refinance`).
   - Order the result by `user_id` in ascending order.

3. **Key SQL Concepts:**
   - Filtering using `WHERE`.
   - Aggregation using `GROUP BY` and `COUNT(DISTINCT column)`.
   - Filtering groups with `HAVING`.

---

### Query

```sql
SELECT user_id 
FROM loans
WHERE loan_type IN ('Mortgage', 'Refinance')
GROUP BY user_id
HAVING COUNT(DISTINCT loan_type) >= 2
ORDER BY user_id;
```

---

### Explanation of the Query

1. **Filter Rows:**
   - The `WHERE loan_type IN ('Mortgage', 'Refinance')` condition filters the rows to include only those with loan types `Mortgage` or `Refinance`.

2. **Group by User:**
   - The `GROUP BY user_id` groups the rows by each `user_id` to analyze their loan types collectively.

3. **Check Conditions:**
   - The `HAVING COUNT(DISTINCT loan_type) >= 2` ensures that only users with both `Mortgage` and `Refinance` loan types are included.

4. **Order the Result:**
   - The `ORDER BY user_id` arranges the output in ascending order by `user_id`.

---

### Why This Output?
- The query ensures that only users with both `Mortgage` and `Refinance` loans are included in the result.
- For the given example, `user_id 102` satisfies this condition, so it is included in the output.
