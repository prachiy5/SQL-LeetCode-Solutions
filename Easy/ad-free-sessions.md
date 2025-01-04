# Ad-Free Sessions

### Problem

#### Table: Playback

| Column Name | Type |
|-------------|------|
| session_id  | int  |
| customer_id | int  |
| start_time  | int  |
| end_time    | int  |

- `session_id` is the column with unique values for this table.
- `customer_id` is the ID of the customer watching this session.
- The session runs during the inclusive interval between `start_time` and `end_time`.
- It is guaranteed that `start_time <= end_time` and that two sessions for the same customer do not intersect.

#### Table: Ads

| Column Name | Type |
|-------------|------|
| ad_id       | int  |
| customer_id | int  |
| timestamp   | int  |

- `ad_id` is the column with unique values for this table.
- `customer_id` is the ID of the customer viewing this ad.
- `timestamp` is the moment of time at which the ad was shown.

#### Task
Write a solution to report all the sessions that did not get shown any ads.

Return the result table in any order.

#### Example

**Input:**

`Playback` table:

| session_id | customer_id | start_time | end_time |
|------------|-------------|------------|----------|
| 1          | 1           | 1          | 5        |
| 2          | 1           | 15         | 23       |
| 3          | 2           | 10         | 12       |
| 4          | 2           | 17         | 28       |
| 5          | 2           | 2          | 8        |

`Ads` table:

| ad_id | customer_id | timestamp |
|-------|-------------|-----------|
| 1     | 1           | 5         |
| 2     | 2           | 17        |
| 3     | 2           | 20        |

**Output:**

| session_id |
|------------|
| 2          |
| 3          |
| 5          |

**Explanation:**
- The ad with `ad_id = 1` was shown to user 1 at `timestamp = 5` during session 1.
- The ads with `ad_id = 2` and `ad_id = 3` were shown to user 2 during session 4.
- Sessions 1 and 4 had at least one ad. Sessions 2, 3, and 5 did not have any ads, so we return them.

---

### Thought Process

1. **Identify Sessions Without Ads:**
   - Join the `Playback` table with the `Ads` table on `customer_id`.
   - Ensure that the `timestamp` of the ad falls between the `start_time` and `end_time` of the session.
   - Use a `LEFT JOIN` to include all sessions, even those without matching ads.

2. **Filter Out Sessions with Ads:**
   - Check for `NULL` values in the `ad_id` column to find sessions without any ads.

3. **Select Distinct Session IDs:**
   - Retrieve only the `session_id` values for sessions that did not display ads.

---

### Solution

```sql
SELECT DISTINCT pb.session_id
FROM Playback pb
LEFT JOIN Ads ad
  ON pb.customer_id = ad.customer_id
  AND ad.timestamp BETWEEN pb.start_time AND pb.end_time
WHERE ad.ad_id IS NULL;
```

---

### Solution Explanation

- **LEFT JOIN:** Combines `Playback` and `Ads` tables, matching `customer_id` and ensuring the ad's `timestamp` falls within the session's `start_time` and `end_time`.
- **Filter with `WHERE`:** Filters rows where `ad_id` is `NULL`, meaning no ads were shown during the session.
- **DISTINCT:** Ensures unique `session_id` values in the result.

---

### Key Takeaways

- Use `LEFT JOIN` to find unmatched rows between two tables.
- Use `NULL` checks to identify rows without matches.
- Use `DISTINCT` to remove duplicate entries from the result set.

---

### Related Topics

- SQL Joins (INNER, LEFT, RIGHT, FULL)
- Filtering with NULL values
- Using DISTINCT in SQL
