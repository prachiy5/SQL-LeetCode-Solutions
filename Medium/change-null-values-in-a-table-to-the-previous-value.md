### Change Null Values in a Table to the Previous Value

We are given a table:

#### Table: CoffeeShop
| Column Name | Type    |
|-------------|---------|
| id          | int     |
| drink       | varchar |

- `id` is the primary key, meaning it contains unique values for each row.
- Each row represents an order ID and the name of the drink ordered.
- Some rows have `null` values in the `drink` column.
- It is guaranteed that the first row of the table has a non-null value for the `drink` column.

#### Task
Write a query to replace the `null` values in the `drink` column with the most recent non-null value in the previous rows. The result table should retain the same order as the input.

#### Example Input/Output

##### Input:
**CoffeeShop table:**
| id | drink             |
|----|-------------------|
| 9  | Rum and Coke      |
| 6  | null              |
| 7  | null              |
| 3  | St Germain Spritz |
| 1  | Orange Margarita  |
| 2  | null              |

##### Output:
| id | drink             |
|----|-------------------|
| 9  | Rum and Coke      |
| 6  | Rum and Coke      |
| 7  | Rum and Coke      |
| 3  | St Germain Spritz |
| 1  | Orange Margarita  |
| 2  | Orange Margarita  |

##### Explanation:
- For ID 6, the previous non-null value is from ID 9: `Rum and Coke`.
- For ID 7, the previous non-null value is also from ID 9: `Rum and Coke`.
- For ID 2, the previous non-null value is from ID 1: `Orange Margarita`.
- The result retains the original row order.

---

### Thought Process
1. Use a `SUM()` window function to create a group identifier (`grp`) for each consecutive set of rows with the same preceding non-null value.
   - Increment the group identifier whenever a non-null value is encountered.
   - Use `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` to calculate the cumulative sum.
2. Assign a row number (`rn`) to each row to retain the original row order.
3. Use the `FIRST_VALUE()` window function to fetch the first non-null value for each group (`grp`), ordered by the row number (`rn`).
4. Return the updated `drink` values along with the original `id` column.

---

### Query
```sql
WITH cte AS (
    SELECT *,
           SUM(CASE WHEN drink IS NOT NULL THEN 1 ELSE 0 END) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS grp,
           ROW_NUMBER() OVER () AS rn
    FROM CoffeeShop
)

SELECT id, 
       FIRST_VALUE(drink) OVER (PARTITION BY grp ORDER BY rn) AS drink
FROM cte;
```

---

### Key Takeaways
- **Window Functions:**
  - `SUM()` is used to create a cumulative group identifier for rows with the same preceding non-null value.
  - `FIRST_VALUE()` fetches the first non-null value in each group.
- **Null Handling:** The logic ensures that `null` values are replaced by the most recent non-null value.
- **Order Preservation:** Assigning row numbers (`ROW_NUMBER()`) ensures the result retains the original input order.

---

### Related Topics
- SQL Window Functions
- Null Handling in SQL
- Query Optimization with CTEs
