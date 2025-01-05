### Number of Accounts That Did Not Stream

You are provided with two tables:

#### Table: Subscriptions

| Column Name | Type |
|-------------|------|
| account_id  | int  |
| start_date  | date |
| end_date    | date |

- `account_id` is the primary key column for this table.
- Each row of this table indicates the start and end dates of an account's subscription.
- It is guaranteed that `start_date < end_date`.

#### Table: Streams

| Column Name | Type |
|-------------|------|
| session_id  | int  |
| account_id  | int  |
| stream_date | date |

- `session_id` is the primary key column for this table.
- `account_id` is a foreign key from the Subscriptions table.
- Each row of this table contains information about the account and the date associated with a stream session.

**Task:** Write an SQL query to report the number of accounts that bought a subscription in 2021 but did not have any stream session. 

The query result should be returned in the following format:

| accounts_count |
|----------------|
| count_value    |

---

### Example Input:

#### Subscriptions table:

| account_id | start_date | end_date   |
|------------|------------|------------|
| 9          | 2020-02-18 | 2021-10-30 |
| 3          | 2021-09-21 | 2021-11-13 |
| 11         | 2020-02-28 | 2020-08-18 |
| 13         | 2021-04-20 | 2021-09-22 |
| 4          | 2020-10-26 | 2021-05-08 |
| 5          | 2020-09-11 | 2021-01-17 |

#### Streams table:

| session_id | account_id | stream_date |
|------------|------------|-------------|
| 14         | 9          | 2020-05-16  |
| 16         | 3          | 2021-10-27  |
| 18         | 11         | 2020-04-29  |
| 17         | 13         | 2021-08-08  |
| 19         | 4          | 2020-12-31  |
| 13         | 5          | 2021-01-05  |

### Output:

| accounts_count |
|----------------|
| 2              |

---

### Thought Process

1. **Identify relevant subscriptions:**
   - First, filter the `Subscriptions` table to find accounts with subscriptions that ended in 2021 (`YEAR(end_date) = 2021`).

2. **Find accounts with streams in 2021:**
   - Join the filtered subscriptions with the `Streams` table and filter for stream dates in 2021 (`YEAR(stream_date) = 2021`).

3. **Exclude accounts with streams in 2021:**
   - Using the filtered subscriptions, exclude accounts that appear in the above result to get accounts without streams in 2021.

4. **Count the result:**
   - Count the distinct accounts from the filtered list.

---

### Query

```sql
WITH cte AS (
    SELECT DISTINCT account_id
    FROM subscriptions
    WHERE YEAR(end_date) = 2021
)

SELECT COUNT(DISTINCT cte.account_id) AS accounts_count
FROM cte
LEFT JOIN streams s 
ON cte.account_id = s.account_id AND YEAR(s.stream_date) = 2021
WHERE s.account_id IS NULL;
