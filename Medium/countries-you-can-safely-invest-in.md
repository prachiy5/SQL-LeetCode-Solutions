#### Countries You Can Safely Invest In

Table `Person`:
```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| id             | int     |
| name           | varchar |
| phone_number   | varchar |
+----------------+---------+
```
`id` is the column of unique values for this table.
Each row of this table contains the name of a person and their phone number.
Phone number will be in the form 'xxx-yyyyyyy' where `xxx` is the country code (3 characters) and `yyyyyyy` is the phone number (7 characters) where `x` and `y` are digits. Both can contain leading zeros.

Table `Country`:
```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| name           | varchar |
| country_code   | varchar |
+----------------+---------+
```
`country_code` is the column of unique values for this table.
Each row of this table contains the country name and its code. `country_code` will be in the form 'xxx' where `x` is digits.

Table `Calls`:
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| caller_id   | int  |
| callee_id   | int  |
| duration    | int  |
+-------------+------+
```
This table may contain duplicate rows.
This table contains the account id of the user who logged in and the login date. A user may log in multiple times in the day.

Active users are those who logged in to their accounts for five or more consecutive days.

Write a solution to find the countries where this company can invest.

Return the result table in any order.

The result format is in the following example.

**Example 1:**

**Input:**

`Person` table:

```
+----+----------+--------------+
| id | name     | phone_number |
+----+----------+--------------+
| 3  | Jonathan | 051-1234567  |
| 12 | Elvis    | 051-7654321  |
| 1  | Moncef   | 212-1234567  |
| 2  | Maroua   | 212-6523651  |
| 7  | Meir     | 972-1234567  |
| 9  | Rachel   | 972-0011100  |
+----+----------+--------------+
```

`Country` table:

```
+----------+--------------+
| name     | country_code |
+----------+--------------+
| Peru     | 051          |
| Israel   | 972          |
| Morocco  | 212          |
| Germany  | 049          |
| Ethiopia | 251          |
+----------+--------------+
```

`Calls` table:

```
+-----------+-----------+----------+
| caller_id | callee_id | duration |
+-----------+-----------+----------+
| 1         | 9         | 33       |
| 2         | 9         | 4        |
| 1         | 2         | 59       |
| 3         | 12        | 102      |
| 3         | 12        | 330      |
| 12        | 3         | 5        |
| 7         | 9         | 13       |
| 7         | 1         | 3        |
| 9         | 7         | 1        |
| 1         | 7         | 7        |
+-----------+-----------+----------+
```

**Output:**

```
+----------+
| country  |
+----------+
| Peru     |
+----------+
```

**Explanation:**

The average call duration for Peru is `(102 + 102 + 330 + 330 + 5 + 5) / 6 = 145.666667`
The average call duration for Israel is `(33 + 4 + 13 + 13 + 3 + 1 + 1 + 7) / 8 = 9.37500`
The average call duration for Morocco is `(33 + 4 + 59 + 59 + 3 + 7) / 6 = 27.5000 `
Global call duration average = `(2 * (33 + 4 + 59 + 102 + 330 + 5 + 13 + 3 + 1 + 7)) / 20 = 55.70000`
Since Peru is the only country where the average call duration is greater than the global average, it is the only recommended country.

---

**Thought Process:**
1. Extract the country code from phone numbers in the `Person` table.
2. Join the `Person` table with the `Country` table to map country names to phone numbers.
3. Combine the resulting data with the `Calls` table to calculate call durations by country.
4. Compute the average call duration globally.
5. Compare each country's average call duration with the global average.
6. Return countries where the average call duration is greater than the global average.

---

**Query:**

```sql
WITH cte AS (
    SELECT id, name, phone_number, 
           SUBSTRING(phone_number, 1, 3) AS country_code
    FROM person
),

cte2 AS (
    SELECT calls.caller_id AS caller_id,  calls.callee_id AS callee_id, duration, c.name AS country, c.country_code
    FROM cte 
    INNER JOIN country c 
        ON cte.country_code = c.country_code
    INNER JOIN calls 
        ON id = caller_id OR id = callee_id
)

SELECT country 
FROM cte2
GROUP BY country
HAVING AVG(duration) > (
    SELECT AVG(duration) 
    FROM calls
);
```
