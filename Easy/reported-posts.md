# Reported Posts
### Problem

#### Table Overview

**Actions**

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| post_id       | int     |
| action_date   | date    |
| action        | enum    |
| extra         | varchar |

- This table may contain duplicate rows.
- The `action` column is an ENUM with values: `'view', 'like', 'reaction', 'comment', 'report', 'share'`.
- The `extra` column provides additional information about the action (e.g., the reason for a report) and is never `NULL`.

#### Task
Write a query to report the number of posts reported yesterday for each report reason.
- Assume **today** is `2019-07-05`.
- Only include report reasons with non-zero counts.

---

### Example

**Input:**

`Actions` table:

| user_id | post_id | action_date | action | extra  |
|---------|---------|-------------|--------|--------|
| 1       | 1       | 2019-07-01  | view   | null   |
| 1       | 1       | 2019-07-01  | like   | null   |
| 1       | 1       | 2019-07-01  | share  | null   |
| 2       | 4       | 2019-07-04  | view   | null   |
| 2       | 4       | 2019-07-04  | report | spam   |
| 3       | 4       | 2019-07-04  | view   | null   |
| 3       | 4       | 2019-07-04  | report | spam   |
| 4       | 3       | 2019-07-02  | view   | null   |
| 4       | 3       | 2019-07-02  | report | spam   |
| 5       | 2       | 2019-07-04  | view   | null   |
| 5       | 2       | 2019-07-04  | report | racism |
| 5       | 5       | 2019-07-04  | view   | null   |
| 5       | 5       | 2019-07-04  | report | racism |

**Output:**

| report_reason | report_count |
|---------------|--------------|
| spam          | 1            |
| racism        | 2            |

**Explanation:**
- The query focuses on reports made on `2019-07-04` (yesterday).
- Posts with `spam` were reported once, and `racism` was reported twice.

---

### Thought Process

1. **Filter for Reports from Yesterday:**
   - Use the `action_date` column to filter rows for `2019-07-04`.
   - Use the `action` column to filter rows where the action is `'report'`.

2. **Group by Report Reason:**
   - Group rows by the `extra` column, which contains the report reason.

3. **Count Unique Posts for Each Reason:**
   - Use `COUNT(DISTINCT post_id)` to calculate the number of unique posts reported for each reason.

4. **Return the Result:**
   - Select `extra` as `report_reason` and the count as `report_count`.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT 
    extra AS report_reason, 
    COUNT(DISTINCT post_id) AS report_count
FROM actions
WHERE 
    action_date = DATE_SUB('2019-07-05', INTERVAL 1 DAY)
    AND action = 'report'
GROUP BY extra;
```

---

### Solution Explanation

1. **Filter Rows:**
   - The `WHERE` clause filters rows where `action_date` equals `2019-07-04` and `action` equals `'report'`.

2. **Group by Reason:**
   - The `GROUP BY extra` groups rows by the `extra` column (report reason).

3. **Count Unique Posts:**
   - The `COUNT(DISTINCT post_id)` ensures each post is only counted once per reason.

4. **Result Columns:**
   - The query selects `extra` (renamed as `report_reason`) and the count of posts (`report_count`).

---

### Key Takeaways

- **Filtering Dates:** Use `DATE_SUB()` to dynamically calculate dates relative to a reference date.
- **Grouping and Aggregation:** Use `GROUP BY` with aggregate functions to summarize data.
- **Distinct Counts:** Use `COUNT(DISTINCT column)` to count unique values.

---

### Related Topics
- SQL Date Functions
- Aggregate Functions (COUNT, SUM)
- Filtering with WHERE and GROUP BY
