### Find All Unique Email Domains

#### Table: Emails

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
```

- **id** is the primary key (column with unique values) for this table.
- Each row of this table contains an email. The emails will not contain uppercase letters.

Write a solution to find all unique email domains and count the number of individuals associated with each domain. Consider only those domains that end with `.com`.

Return the result table ordered by email domains in ascending order.

The result format is in the following example.

#### Example 1:

**Input:** 

```
Emails table:
+-----+-----------------------+
| id  | email                 |
+-----+-----------------------+
| 336 | hwkiy@test.edu        |
| 489 | adcmaf@outlook.com    |
| 449 | vrzmwyum@yahoo.com    |
| 95  | tof@test.edu          |
| 320 | jxhbagkpm@example.org |
| 411 | zxcf@outlook.com      |
+-----+-----------------------+
```

**Output:** 

```
+--------------+-------+
| email_domain | count |
+--------------+-------+
| outlook.com  | 2     |
| yahoo.com    | 1     |
+--------------+-------+
```

**Explanation:**
- The valid domains ending with `.com` are only `outlook.com` and `yahoo.com`, with respective counts of `2` and `1`.
- Output table is ordered by `email_domains` in ascending order.

---

### Thought Process:

1. **Understand the Requirement:**
   - Extract the domain part of the email addresses using the `SUBSTRING_INDEX` function.
   - Filter only those domains that end with `.com` using the `LIKE` operator.
   - Group by the extracted domain and count the number of unique IDs for each domain.

2. **Step-by-Step Plan:**
   - Use a Common Table Expression (CTE) to extract the domain from the email using the `SUBSTRING_INDEX` function.
   - Use another CTE to filter out domains that end with `.com`.
   - Count the occurrences of each valid domain using the `COUNT` function.
   - Order the results by domain name in ascending order.

3. **Expected Output:**
   - A table showing the `email_domain` and the corresponding count of users with that domain.

---

### Query:

```sql
WITH CTE AS (
    SELECT id, SUBSTRING_INDEX(email, '@', -1) AS domain
    FROM emails
),

cte2 AS (
    SELECT id, domain AS email_domain
    FROM CTE
    WHERE domain LIKE '%.com'
)

SELECT email_domain, COUNT(id) AS count
FROM cte2
GROUP BY email_domain
ORDER BY email_domain;
```

---

### Explanation of the Query:

1. **Extracting the Domain:**
   - `SUBSTRING_INDEX(email, '@', -1)` extracts everything after the `@` symbol in the email, giving us the domain part.
   - This result is stored in the first Common Table Expression (CTE) named `CTE`.

2. **Filtering `.com` Domains:**
   - `WHERE domain LIKE '%.com'` filters only the domains that end with `.com`.
   - These filtered results are stored in the second CTE named `cte2`.

3. **Counting the Domains:**
   - `COUNT(id)` calculates the number of users associated with each domain.
   - `GROUP BY email_domain` ensures that the count is grouped by each unique domain.

4. **Sorting the Result:**
   - `ORDER BY email_domain` ensures that the domains are sorted alphabetically in ascending order.

---

### Why this Output?

- From the input data, only `outlook.com` and `yahoo.com` are valid `.com` domains.
  - `outlook.com` appears twice.
  - `yahoo.com` appears once.
- The result table reflects this count, sorted by domain name in ascending order.

**Final Output:**

```
+--------------+-------+
| email_domain | count |
+--------------+-------+
| outlook.com  | 2     |
| yahoo.com    | 1     |
+--------------+-------+
