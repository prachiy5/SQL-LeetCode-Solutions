### Friday Purchases I

We are given a table:

#### Table: Purchases
| Column Name   | Type |
|---------------|------|
| user_id       | int  |
| purchase_date | date |
| amount_spend  | int  |

- `(user_id, purchase_date, amount_spend)` is the primary key.
- `purchase_date` includes dates only from November 1, 2023, to November 30, 2023.
- Each row contains:
  - `user_id`: The ID of the user.
  - `purchase_date`: The date of the purchase.
  - `amount_spend`: The amount spent on the purchase.

#### Task
Write a query to calculate the total spending by users on each Friday of every week in November 2023. Only include weeks with at least one purchase on a Friday in the result.

The result table should include:
- `week_of_month`: The week of the month (1 for the first week, 2 for the second, etc.).
- `purchase_date`: The date of the Friday.
- `total_amount`: The total amount spent on that Friday.

The result should be ordered by `week_of_month` in ascending order.

---

### Example Input/Output

##### Input:
**Purchases table:**
| user_id | purchase_date | amount_spend |
|---------|---------------|--------------|
| 11      | 2023-11-07    | 1126         |
| 15      | 2023-11-30    | 7473         |
| 17      | 2023-11-14    | 2414         |
| 12      | 2023-11-24    | 9692         |
| 8       | 2023-11-03    | 5117         |
| 1       | 2023-11-16    | 5241         |
| 10      | 2023-11-12    | 8266         |
| 13      | 2023-11-24    | 12000        |

##### Output:
| week_of_month | purchase_date | total_amount |
|---------------|---------------|--------------|
| 1             | 2023-11-03    | 5117         |
| 4             | 2023-11-24    | 21692        |

##### Explanation:
- **Week 1:**
  - Friday: November 3, 2023.
  - Total spending: $5,117.
- **Week 2 and Week 3:**
  - No purchases on Fridays.
- **Week 4:**
  - Friday: November 24, 2023.
  - Total spending: $9,692 + $12,000 = $21,692.

---

### Thought Process
1. **Identify Fridays:**
   - Use `DAYNAME()` to determine the day of the week for each `purchase_date`.
2. **Determine Week of the Month:**
   - Use the formula:
     ```
     WEEK(purchase_date, 5) - WEEK(DATE_SUB(purchase_date, INTERVAL DAYOFMONTH(purchase_date) - 1 DAY), 5) + 1
     ```
     to calculate the week of the month.
3. **Filter Fridays:**
   - Include only rows where the day of the week is `Friday`.
4. **Calculate Total Spending per Friday:**
   - Group by `week_of_month` and sum the `amount_spend` for each Friday.
5. **Order the Results:**
   - Order by `week_of_month` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT user_id,
           purchase_date,
           amount_spend,
           DAYNAME(purchase_date) AS day_of_week,
           WEEK(purchase_date, 5) - WEEK(DATE_SUB(purchase_date, INTERVAL DAYOFMONTH(purchase_date) - 1 DAY), 5) + 1 AS week_of_month
    FROM purchases
)

SELECT week_of_month,
       purchase_date,
       SUM(amount_spend) AS total_amount
FROM cte
WHERE day_of_week = 'Friday'
GROUP BY week_of_month, purchase_date
ORDER BY week_of_month ASC;
```

---

### Key Takeaways
- **Day of the Week:**
  - `DAYNAME()` helps identify Fridays.
- **Week Calculation:**
  - The formula to calculate `week_of_month` ensures correct grouping for weeks within a specific month.
- **Grouping and Summation:**
  - Grouping by `week_of_month` and summing `amount_spend` aggregates spending for each Friday.
- **Filtering Rows:**
  - Only include rows where the day is `Friday`.
- **Ordering Results:**
  - Ensures the result is displayed in the correct order by `week_of_month`.

---

### Related Topics
- Date and Time Functions in SQL
- Conditional Filtering with `WHERE`
- Aggregation (`SUM`) and Grouping
