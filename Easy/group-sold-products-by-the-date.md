# Group Sold Products by the Date

### Problem

#### Table: Activities

| Column Name | Type    |
|-------------|---------|
| sell_date   | date    |
| product     | varchar |

- The table does not have a primary key and may contain duplicates.
- Each row contains the product name and the date it was sold in a market.

### Task

For each date:
1. Find the number of distinct products sold (`num_sold`).
2. List the names of the distinct products, sorted lexicographically, separated by commas.

Return the result table ordered by `sell_date`.

### Example

#### Input

**Activities** table:

| sell_date  | product     |
|------------|-------------|
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |

#### Output

| sell_date  | num_sold | products                     |
|------------|----------|------------------------------|
| 2020-05-30 | 3        | Basketball,Headphone,T-Shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |

#### Explanation

1. For **2020-05-30**, the sold items are `Headphone`, `Basketball`, and `T-Shirt`. Sorted lexicographically: `Basketball,Headphone,T-Shirt`.
2. For **2020-06-01**, the sold items are `Pencil` and `Bible`. Sorted lexicographically: `Bible,Pencil`.
3. For **2020-06-02**, the only sold item is `Mask`.

---

### Thought Process

1. **Find Distinct Products:**
   - Use `DISTINCT` to filter out duplicate product names for the same date.

2. **Count Products:**
   - Use `COUNT(DISTINCT product)` to count the number of unique products for each date.

3. **Group Products Lexicographically:**
   - Use `GROUP_CONCAT` to concatenate product names, sorted alphabetically (`ORDER BY product`).

4. **Group and Order by Date:**
   - Use `GROUP BY sell_date` to group results by date.
   - Use `ORDER BY sell_date` to ensure the result is ordered by date.

---

### Solution

```sql
SELECT 
    sell_date, 
    COUNT(DISTINCT product) AS num_sold, 
    GROUP_CONCAT(DISTINCT product ORDER BY product) AS products
FROM Activities
GROUP BY sell_date
ORDER BY sell_date;
```

---

### Solution Explanation

1. **COUNT(DISTINCT product):**
   - Counts the number of unique products for each date.

2. **GROUP_CONCAT:**
   - Concatenates distinct product names for each date, sorted alphabetically (`ORDER BY product`).

3. **GROUP BY:**
   - Groups rows by `sell_date` to calculate aggregates (`num_sold` and `products`) for each date.

4. **ORDER BY:**
   - Sorts the final output by `sell_date`.

---

### Key Takeaways

- **GROUP_CONCAT:** Use it to concatenate values from multiple rows into a single string.
- **DISTINCT:** Filter out duplicate values when counting or grouping.
- **Grouping and Sorting:** Always `GROUP BY` and `ORDER BY` to organize results as needed.

---

### Related Topics

- SQL Aggregation
- String Functions (e.g., `GROUP_CONCAT`)
- Working with Duplicates
