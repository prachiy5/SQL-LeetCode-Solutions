### Question

Table: Ads

```plaintext
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ad_id         | int     |
| user_id       | int     |
| action        | enum    |
+---------------+---------+
```

- `(ad_id, user_id)` is the primary key (combination of columns with unique values) for this table.
- Each row of this table contains the ID of an Ad, the ID of a user, and the action taken by this user regarding this Ad.
- The `action` column is an ENUM (category) type of ('Clicked', 'Viewed', 'Ignored').

A company is running Ads and wants to calculate the performance of each Ad.

**Performance of the Ad is measured using Click-Through Rate (CTR) where:**

CTR = (Number of Clicks) / (Number of Clicks + Number of Views) * 100

Write a solution to find the `ctr` of each Ad. Round `ctr` to two decimal points.

Return the result table ordered by `ctr` in descending order and by `ad_id` in ascending order in case of a tie.

The result format is in the following example.

### Example 1:

#### Input: 

Table `Ads`:

```plaintext
+-------+---------+---------+
| ad_id | user_id | action  |
+-------+---------+---------+
| 1     | 1       | Clicked |
| 2     | 2       | Clicked |
| 3     | 3       | Viewed  |
| 5     | 5       | Ignored |
| 1     | 7       | Ignored |
| 2     | 7       | Viewed  |
| 3     | 5       | Clicked |
| 1     | 4       | Viewed  |
| 2     | 11      | Viewed  |
| 1     | 2       | Clicked |
+-------+---------+---------+
```

#### Output:

```plaintext
+-------+-------+
| ad_id | ctr   |
+-------+-------+
| 1     | 66.67 |
| 3     | 50.00 |
| 2     | 33.33 |
| 5     | 0.00  |
+-------+-------+
```

#### Explanation:

- For `ad_id = 1`: CTR = (2 / (2 + 1)) * 100 = 66.67
- For `ad_id = 2`: CTR = (1 / (1 + 2)) * 100 = 33.33
- For `ad_id = 3`: CTR = (1 / (1 + 1)) * 100 = 50.00
- For `ad_id = 5`: CTR = 0.00 (No clicks or views for this ad).

We do not count ignored actions.

---

### Thought Process

1. **Filter Relevant Actions:** We only care about actions classified as 'Clicked' or 'Viewed'. Ignored actions are irrelevant for the CTR calculation.
2. **Calculate Clicks and Views:**
   - Count the number of 'Clicked' actions for each `ad_id`.
   - Count the total number of 'Clicked' and 'Viewed' actions for each `ad_id`.
3. **Compute CTR:** Divide the number of clicks by the sum of clicks and views, then multiply by 100 to get the percentage.
4. **Handle Edge Cases:**
   - If there are no clicks or views for an ad, the CTR should be 0.
   - Use `IFNULL` to handle cases where the denominator might be 0.
5. **Ordering:**
   - Sort the result by `ctr` in descending order.
   - In case of ties, sort by `ad_id` in ascending order.

---

### Query

```sql
SELECT 
    ad_id,
    IFNULL(
        ROUND(
            SUM(CASE WHEN action = 'Clicked' THEN 1 ELSE 0 END) / 
            SUM(CASE WHEN action IN ('Clicked', 'Viewed') THEN 1 ELSE 0 END) * 100,
            2
        ),
        0
    ) AS ctr
FROM 
    Ads
GROUP BY 
    ad_id
ORDER BY 
    ctr DESC, 
    ad_id ASC;
```
