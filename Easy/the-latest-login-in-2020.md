# The Latest Login in 2020
### Problem Description

We are given a table `Logins` that records login timestamps for various users. Our task is to find the **latest login** for each user in the year **2020**. Users who did not log in during 2020 should not be included in the result.

### Table Details

#### Logins Table:
| Column Name | Type     | Description                          |
|-------------|----------|--------------------------------------|
| `user_id`   | `int`    | The unique ID of the user.           |
| `time_stamp`| `datetime`| The timestamp of the user's login.   |

- `(user_id, time_stamp)` is the primary key for the table.
---

#### Input:
| `user_id` | `time_stamp`          |
|-----------|-----------------------|
| 6         | 2020-06-30 15:06:07  |
| 6         | 2021-04-21 14:06:06  |
| 6         | 2019-03-07 00:18:15  |
| 8         | 2020-02-01 05:10:53  |
| 8         | 2020-12-30 00:46:50  |
| 2         | 2020-01-16 02:49:50  |
| 2         | 2019-08-25 07:59:08  |
| 14        | 2019-07-14 09:00:00  |
| 14        | 2021-01-06 11:59:59  |

#### Output:
| `user_id` | `last_stamp`          |
|-----------|-----------------------|
| 6         | 2020-06-30 15:06:07  |
| 8         | 2020-12-30 00:46:50  |
| 2         | 2020-01-16 02:49:50  |

---

### Task

For each user who logged in during **2020**, find their **latest login timestamp** within the year. Exclude users who did not log in during this time.

---

### Solution

#### Thought Process:
1. **Filter logins for the year 2020:** Use the `YEAR()` function to extract the year from the `time_stamp` column and only include rows where the year is `2020`.
2. **Find the latest login for each user:** Use the `MAX()` function grouped by `user_id` to get the most recent `time_stamp` for each user in the filtered data.
3. **Exclude users without 2020 logins:** Since we filter rows by year in the `WHERE` clause, users without logins in 2020 are automatically excluded.
4. **Return results:** Select the `user_id` and their corresponding `last_stamp` (latest login time).

#### SQL Query:

```sql
SELECT 
    user_id, 
    MAX(time_stamp) AS last_stamp
FROM 
    logins
WHERE 
    YEAR(time_stamp) = 2020
GROUP BY 
    user_id;
```

---

### Explanation of the Query:

1. **Filter for 2020 logins:**  
   `WHERE YEAR(time_stamp) = 2020` ensures that only logins in the year 2020 are considered.

2. **Group by user_id:**  
   `GROUP BY user_id` ensures that the results are aggregated for each unique user.

3. **Find the latest login timestamp:**  
   `MAX(time_stamp)` calculates the most recent login for each user within the filtered data.

4. **Exclude users without 2020 logins:**  
   Since the query filters for `YEAR(time_stamp) = 2020`, users with no logins in 2020 are not part of the result set.

---


### Key Takeaways:
- Use `YEAR()` to filter datetime values by year.
- Use `MAX()` to find the latest value in a group.
- Use `GROUP BY` to calculate aggregated results for each unique entity (in this case, `user_id`).
