### Investments in 2016

Table: Insurance

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| pid         | int   |
| tiv_2015    | float |
| tiv_2016    | float |
| lat         | float |
| lon         | float |
+-------------+-------+
```
- `pid` is the primary key (column with unique values) for this table.
- Each row of this table contains information about one policy where:
  - `pid` is the policyholder's policy ID.
  - `tiv_2015` is the total investment value in 2015.
  - `tiv_2016` is the total investment value in 2016.
  - `lat` is the latitude of the policy holder's city. It's guaranteed that `lat` is not NULL.
  - `lon` is the longitude of the policy holder's city. It's guaranteed that `lon` is not NULL.

Write a solution to report the sum of all total investment values in 2016 (`tiv_2016`), for all policyholders who:

1. Have the same `tiv_2015` value as one or more other policyholders, and
2. Are not located in the same city as any other policyholder (i.e., the `(lat, lon)` attribute pairs must be unique).

Round `tiv_2016` to two decimal places.

The result format is in the following example.

### Example

**Input:**

`Insurance` table:

| pid | tiv_2015 | tiv_2016 | lat | lon |
|-----|----------|----------|-----|-----|
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |

**Output:**

| tiv_2016 |
|----------|
| 45.00    |

**Explanation:**

- The first record (pid = 1) and the last record (pid = 4) meet both of the criteria:
  - Their `tiv_2015` value is 10, which is the same as the third and fourth records.
  - Their `(lat, lon)` values are unique.
- The second record (pid = 2) does not meet the criteria because its `tiv_2015` is unique and its location is the same as the third record.
- The third record (pid = 3) fails because its location is the same as the second record.
- The result is the sum of `tiv_2016` for the first and last records: `5 + 40 = 45.00`.

### Thought Process

1. **Understand the Requirements:**
   - We need to find policyholders who share the same `tiv_2015` with others but are located in unique cities.
   - Compute the sum of their `tiv_2016` values, rounded to two decimal places.

2. **Break Down the Criteria:**
   - First, identify `tiv_2015` values that appear more than once.
   - Then, find unique `(lat, lon)` pairs.

3. **Plan the Query:**
   - Use a Common Table Expression (CTE) or subquery to identify `tiv_2015` values that occur more than once.
   - Use another CTE to find unique `(lat, lon)` pairs.
   - Filter the `Insurance` table to include only rows that satisfy both conditions.
   - Sum the `tiv_2016` values and round the result.

4. **Write the Query:**
   - Use `GROUP BY` to find duplicates and unique locations.
   - Use `IN` clauses to filter records that meet both conditions.

### Query

```sql
WITH cte1 AS (
    SELECT tiv_2015
    FROM Insurance
    GROUP BY tiv_2015
    HAVING COUNT(*) > 1
),
cte2 AS (
    SELECT lat, lon
    FROM Insurance
    GROUP BY lat, lon
    HAVING COUNT(*) = 1
)
SELECT ROUND(SUM(tiv_2016), 2) AS tiv_2016
FROM Insurance
WHERE tiv_2015 IN (SELECT tiv_2015 FROM cte1)
  AND (lat, lon) IN (SELECT lat, lon FROM cte2);
```

### Explanation

1. **Identify Duplicate `tiv_2015` Values:**
   - Use `GROUP BY` and `HAVING COUNT(*) > 1` to find `tiv_2015` values shared by multiple records.

2. **Find Unique Locations:**
   - Use `GROUP BY lat, lon` and `HAVING COUNT(*) = 1` to isolate unique `(lat, lon)` pairs.

3. **Filter and Sum:**
   - Filter rows where `tiv_2015` matches any duplicate values and `(lat, lon)` is unique.
   - Calculate the sum of `tiv_2016` for these rows and round it to two decimal places.

### Output

| tiv_2016 |
|----------|
| 45.00    |
