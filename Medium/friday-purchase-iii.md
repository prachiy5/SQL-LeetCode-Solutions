### Friday Purchase III 

We have two tables:

#### Table: Purchases
| Column Name   | Type |
|---------------|------|
| user_id       | int  |
| purchase_date | date |
| amount_spend  | int  |

- `user_id`, `purchase_date`, and `amount_spend` form the primary key.
- This table contains records of purchases made by users, including the date and amount spent.
- `purchase_date` ranges from November 1, 2023, to November 30, 2023.

#### Table: Users
| Column Name | Type |
|-------------|------|
| user_id     | int  |
| membership  | enum |

- `user_id` is the primary key.
- `membership` indicates the membership type of a user and can be one of 'Standard', 'Premium', or 'VIP'.

#### Task
Write a query to calculate the total spending by Premium and VIP members on each Friday in November 2023. If there are no purchases on a particular Friday by Premium or VIP members, the result should show `0` for that category. The result should be ordered by `week_of_month` (ascending) and `membership` (ascending).

---

### Example Input/Output

#### Input:
**Purchases table:**
| user_id | purchase_date | amount_spend |
|---------|---------------|--------------|
| 11      | 2023-11-03    | 1126         |
| 15      | 2023-11-10    | 7473         |
| 17      | 2023-11-17    | 2414         |
| 12      | 2023-11-24    | 9692         |
| 8       | 2023-11-24    | 5117         |
| 1       | 2023-11-24    | 5241         |
| 10      | 2023-11-22    | 8266         |
| 13      | 2023-11-21    | 12000        |

**Users table:**
| user_id | membership |
|---------|------------|
| 11      | Premium    |
| 15      | VIP        |
| 17      | Standard   |
| 12      | VIP        |
| 8       | Premium    |
| 1       | VIP        |
| 10      | Standard   |
| 13      | Premium    |

#### Output:
| week_of_month | membership | total_amount |
|---------------|------------|--------------|
| 1             | Premium    | 1126         |
| 1             | VIP        | 0            |
| 2             | Premium    | 0            |
| 2             | VIP        | 7473         |
| 3             | Premium    | 0            |
| 3             | VIP        | 0            |
| 4             | Premium    | 5117         |
| 4             | VIP        | 14933        |

---

### Thought Process
1. **Determine Weeks in November:** Divide November into four weeks using the day of the month.
2. **Filter by Premium and VIP Membership:** Focus only on Premium and VIP users by joining `Users` with `Purchases`.
3. **Identify Fridays:** Use the `DAYNAME` function to isolate purchases made on Fridays.
4. **Aggregate Spending:** For each `week_of_month` and `membership`, calculate the total amount spent. Use `IFNULL` to handle cases where no purchases occurred, ensuring the result includes zero values for missing combinations.
5. **Combine with All Possible Combinations:** Use a cross-join of weeks and memberships to ensure every combination of `week_of_month` and `membership` is represented.
6. **Sort Results:** Order by `week_of_month` and `membership`.

---

### Query
```sql
WITH weeks AS (
    SELECT *
    FROM (
        SELECT 1 AS week_of_month UNION ALL 
        SELECT 2 UNION ALL
        SELECT 3 UNION ALL
        SELECT 4
    ) AS weeks
),

membership AS (
    SELECT DISTINCT membership
    FROM users
    WHERE membership IN ('Premium', 'VIP')
),

week_membership AS (
    SELECT week_of_month, membership
    FROM weeks
    CROSS JOIN membership
    ORDER BY 1
),

cte AS (
    SELECT 
        p.user_id,
        p.purchase_date,
        p.amount_spend,
        u.membership,
        DAYNAME(p.purchase_date) AS day_of_week,
        CEIL(DAYOFMONTH(p.purchase_date) / 7) AS week_of_month
    FROM purchases p
    INNER JOIN users u ON p.user_id = u.user_id
)

SELECT 
    wm.week_of_month,
    wm.membership,
    IFNULL(SUM(c.amount_spend), 0) AS total_amount
FROM week_membership wm
LEFT JOIN cte c
ON wm.week_of_month = c.week_of_month
   AND wm.membership = c.membership
   AND day_of_week = 'Friday'
GROUP BY 1, 2
ORDER BY 1, 2;
```

---

### Explanation
1. **Weeks and Membership Combinations:**
   - `weeks`: Enumerates the four weeks in November.
   - `membership`: Selects Premium and VIP memberships.
   - `week_membership`: Cross-joins weeks and memberships to cover all possible combinations.

2. **Filter Relevant Purchases:**
   - Join `Purchases` with `Users` to get membership details.
   - Filter for Fridays using `DAYNAME(purchase_date)`.
   - Calculate `week_of_month` using `CEIL(DAYOFMONTH(purchase_date) / 7)`.

3. **Aggregate Spending:**
   - Sum the spending by `week_of_month` and `membership`.
   - Use `IFNULL` to handle cases where no purchases occurred for a given combination.

4. **Sorting:**
   - Order the results by `week_of_month` (ascending) and `membership` (ascending).

---

### Key Takeaways
- **Cross Join:** Used to ensure all combinations of weeks and membership types are included.
- **Date Functions:** `DAYNAME` and `CEIL` simplify working with dates to identify specific days and weeks.
- **NULL Handling:** `IFNULL` ensures that missing data is represented with zeros in the final result.
