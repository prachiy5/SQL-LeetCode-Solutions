### Active Users
We are given two tables:

#### Table: `Accounts`

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key.
- This table contains the account ID and the user name of each account.

#### Table: `Logins`

| Column Name | Type |
|-------------|------|
| id          | int  |
| login_date  | date |

- This table may contain duplicate rows.
- Each row contains the account ID (`id`) of the user who logged in and the date of the login.
- A user may log in multiple times in a single day.

### Task
Find the IDs and names of all **active users**. A user is considered active if they logged in for five or more consecutive days.

- Return the result table ordered by `id`.
- **Follow-up:** Write a general solution if the active users are those who logged in for `n` or more consecutive days.

### Example
#### Input:
**Accounts table:**

| id | name     |
|----|----------|
| 1  | Winston  |
| 7  | Jonathan |

**Logins table:**

| id | login_date |
|----|------------|
| 7  | 2020-05-30 |
| 1  | 2020-05-30 |
| 7  | 2020-05-31 |
| 7  | 2020-06-01 |
| 7  | 2020-06-02 |
| 7  | 2020-06-02 |
| 7  | 2020-06-03 |
| 1  | 2020-06-07 |
| 7  | 2020-06-10 |

#### Output:

| id | name     |
|----|----------|
| 7  | Jonathan |

#### Explanation:
- User `Winston` (ID = 1) logged in only on two different days. Thus, Winston is **not** an active user.
- User `Jonathan` (ID = 7) logged in on six different days, with five of them being consecutive (2020-05-30 to 2020-06-03). Thus, Jonathan is an **active user**.

---

### Thought Process
1. **Remove Duplicate Logins:**
   - Create a distinct dataset to ensure each user has only one record per day.

2. **Rank the Logins:**
   - Assign a sequential rank to each login date for a user. This allows us to detect gaps in consecutive days.

3. **Shift Dates for Grouping:**
   - Subtract the rank from the login date to create a "shifted date." Logins with consecutive days will have the same shifted date.

4. **Count Consecutive Days:**
   - Group by `id` and `shifted_date` to count the number of consecutive days for each user.
   - Filter users who have `5` or more consecutive days.

5. **Join with Accounts Table:**
   - Join the result with the `Accounts` table to retrieve the names of active users.

6. **Order the Results:**
   - Return the result ordered by `id`.

---

### SQL Query
```sql
WITH UniqueLogins AS (
    SELECT DISTINCT id, login_date
    FROM Logins
),
RankedLogins AS (
    SELECT id, login_date,
           ROW_NUMBER() OVER (PARTITION BY id ORDER BY login_date) AS rn
    FROM UniqueLogins
),
ConsecutiveLogins AS (
    SELECT id, login_date,
           DATE_SUB(login_date, INTERVAL rn DAY) AS shifted_date
    FROM RankedLogins
),
ActiveUsers AS (
    SELECT id, COUNT(*) AS consecutive_days
    FROM ConsecutiveLogins
    GROUP BY id, shifted_date
    HAVING COUNT(*) >= 5
)

SELECT DISTINCT a.id, a.name
FROM ActiveUsers au
JOIN Accounts a ON au.id = a.id
ORDER BY a.id;
