# Fix Product Name Format

### Problem

#### Table: Sales

| Column Name  | Type    |
|--------------|---------|
| sale_id      | int     |
| product_name | varchar |
| sale_date    | date    |

- `sale_id` is the unique identifier for each sale.
- `product_name` may contain leading and/or trailing spaces, and is case-insensitive.
- `sale_date` is the date of the sale.

### Task

For each product:
1. Normalize `product_name` by:
   - Removing leading and trailing spaces.
   - Converting it to lowercase.
2. Format `sale_date` to `YYYY-MM`.
3. Count the total number of times the product was sold in each month.
4. Order the results by `product_name` in ascending order. If there is a tie, order by `sale_date` in ascending order.

### Example

#### Input

**Sales** table:

| sale_id | product_name | sale_date  |
|---------|--------------|------------|
| 1       | LCPHONE      | 2000-01-16 |
| 2       | LCPhone      | 2000-01-17 |
| 3       | LcPhOnE      | 2000-02-18 |
| 4       | LCKeyCHAiN   | 2000-02-19 |
| 5       | LCKeyChain   | 2000-02-28 |
| 6       | Matryoshka   | 2000-03-31 |

#### Output

| product_name | sale_date | total |
|--------------|-----------|-------|
| lckeychain   | 2000-02   | 2     |
| lcphone      | 2000-01   | 2     |
| lcphone      | 2000-02   | 1     |
| matryoshka   | 2000-03   | 1     |

#### Explanation

1. Normalize `product_name`:
   - `LCPHONE`, `LCPhone`, and `LcPhOnE` become `lcphone`.
   - `LCKeyCHAiN` and `LCKeyChain` become `lckeychain`.
2. Format `sale_date`:
   - Convert `2000-01-16` and `2000-01-17` to `2000-01`.
   - Convert `2000-02-18`, `2000-02-19`, and `2000-02-28` to `2000-02`.
   - Convert `2000-03-31` to `2000-03`.
3. Count sales:
   - `lcphone` sold 2 times in `2000-01` and 1 time in `2000-02`.
   - `lckeychain` sold 2 times in `2000-02`.
   - `matryoshka` sold 1 time in `2000-03`.
4. Order the results:
   - By `product_name` (alphabetically).
   - By `sale_date` (chronologically).

---

### Solution

```sql
SELECT 
    LOWER(TRIM(product_name)) AS product_name,
    DATE_FORMAT(sale_date, '%Y-%m') AS sale_date,
    COUNT(sale_id) AS total
FROM Sales
GROUP BY LOWER(TRIM(product_name)), DATE_FORMAT(sale_date, '%Y-%m')
ORDER BY product_name, sale_date;
```

---

### Solution Explanation

1. **Normalization of `product_name`:**
   - Use `LOWER(TRIM(product_name))` to:
     - Convert all characters to lowercase (`LOWER`).
     - Remove leading and trailing spaces (`TRIM`).

2. **Formatting `sale_date`:**
   - Use `DATE_FORMAT(sale_date, '%Y-%m')` to extract the year and month in `YYYY-MM` format.

3. **Counting Sales:**
   - Use `COUNT(sale_id)` to count the number of sales for each product in each month.

4. **Grouping Data:**
   - Use `GROUP BY LOWER(TRIM(product_name)), DATE_FORMAT(sale_date, '%Y-%m')` to group data by normalized product names and formatted sale dates.

5. **Sorting Results:**
   - Use `ORDER BY product_name, sale_date` to sort by product name alphabetically and by sale date chronologically.

---

### Key Takeaways

- Use string functions like `LOWER` and `TRIM` to normalize text data.
- Use `DATE_FORMAT` to reformat date values for grouping and display.
- Group data by normalized fields to ensure consistent aggregation.
- Sort results based on multiple columns for proper ordering.

---

### Related Topics

- SQL String Functions
- SQL Date Functions
- Aggregation and Grouping
- Sorting Results
