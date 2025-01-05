### The First Day of the Maximum Recorded Degree in Each City

We are given a table named `Weather` that contains the following columns:

| Column Name | Type |
|-------------|------|
| city_id     | int  |
| day         | date |
| degree      | int  |

- `(city_id, day)` is the primary key for this table, meaning each combination of `city_id` and `day` is unique.
- Each row represents the degree of the weather in a city on a specific day during the year 2022.

The task is to write a query to find:
- The day with the maximum recorded degree for each city.
- If multiple days have the same maximum degree for a city, return the earliest day among them.

The result should be ordered by `city_id` in ascending order.

### Example Input/Output

#### Input:
**Weather table:**

| city_id | day        | degree |
|---------|------------|--------|
| 1       | 2022-01-07 | -12    |
| 1       | 2022-03-07 | 5      |
| 1       | 2022-07-07 | 24     |
| 2       | 2022-08-07 | 37     |
| 2       | 2022-08-17 | 37     |
| 3       | 2022-02-07 | -7     |
| 3       | 2022-12-07 | -6     |

#### Output:

| city_id | day        | degree |
|---------|------------|--------|
| 1       | 2022-07-07 | 24     |
| 2       | 2022-08-07 | 37     |
| 3       | 2022-12-07 | -6     |

#### Explanation:
- For city `1`, the maximum degree was recorded on `2022-07-07` with `24` degrees.
- For city `2`, the maximum degree of `37` was recorded on two days: `2022-08-07` and `2022-08-17`. We choose the earlier date: `2022-08-07`.
- For city `3`, the maximum degree of `-6` was recorded on `2022-12-07`.

---

### Thought Process
1. Use a Common Table Expression (CTE) to calculate the **rank** of each day for each city based on the `degree`. Use `DENSE_RANK()` to handle ties in degrees.
2. Partition the data by `city_id` and order by `degree` in descending order and then by `day` in ascending order. This ensures that for ties in `degree`, the earliest `day` is ranked first.
3. Select rows where the rank is `1` for each city, as these represent the days with the maximum degree for each city.
4. Order the final result by `city_id` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY city_id ORDER BY degree DESC, day) AS rn
    FROM weather
)
SELECT city_id, day, degree 
FROM cte
WHERE rn = 1
ORDER BY city_id;
```

---

### Key Takeaways
- **DENSE_RANK()** is used to handle ties while ranking, ensuring that rows with the same value get the same rank.
- The `ORDER BY` clause in the `DENSE_RANK()` function ensures that ties are broken by the earliest date.
- Using a CTE helps organize the query and makes it more readable by separating the ranking logic from the final selection.

---

### Related Topics
- Common Table Expressions (CTEs)
- Window Functions (e.g., `DENSE_RANK()`, `ROW_NUMBER()`)
- Sorting and Ranking in SQL


