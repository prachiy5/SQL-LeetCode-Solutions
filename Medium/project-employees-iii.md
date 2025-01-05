### Project Employees III

#### Problem Description:
We are tasked with identifying the first year of sales for every product in the `Sales` table. For each product, we need to return the `product_id`, the `first_year` of sale, the `quantity` sold, and the `price` per unit for that year. The result should list only one record per product, corresponding to its first year of sales. 

---

#### Schema:

##### Sales Table:
| Column Name | Type  |
|-------------|-------|
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |

- `(sale_id, year)` is the primary key.
- `product_id` is a foreign key referencing the `Product` table.

##### Product Table:
| Column Name  | Type    |
|--------------|---------|
| product_id   | int     |
| product_name | varchar |

- `product_id` is the primary key.

---

#### Example Input:

**Sales Table:**
| sale_id | product_id | year | quantity | price |
|---------|------------|------|----------|-------|
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |

**Product Table:**
| product_id | product_name |
|------------|--------------|
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |

---

#### Expected Output:
| product_id | first_year | quantity | price |
|------------|------------|----------|-------|
| 100        | 2008       | 10       | 5000  |
| 200        | 2011       | 15       | 9000  |

---

#### Thought Process:
1. **Identify the First Year:**
   - Use an aggregate function like `MIN(year)` grouped by `product_id` to find the earliest year a product was sold.
2. **Join with the Sales Table:**
   - After identifying the first year for each product, join this result back with the `Sales` table to retrieve additional information such as `quantity` and `price` for that year.
3. **Filter the Data:**
   - Ensure only records corresponding to the first year of sales are included in the final result.
4. **Return the Results:**
   - Select and return the required columns (`product_id`, `first_year`, `quantity`, `price`).

---

#### SQL Query:

```sql
WITH cte AS (
    SELECT product_id, MIN(year) AS first_year
    FROM sales
    GROUP BY product_id
)

SELECT 
    c.product_id, 
    c.first_year, 
    s.quantity, 
    s.price
FROM 
    sales s
JOIN 
    cte c 
ON 
    c.product_id = s.product_id
    AND s.year = c.first_year;
```

---

#### Explanation of the Query:
1. **CTE (Common Table Expression):**
   - This computes the first year of sales for each product by grouping the `Sales` table by `product_id` and applying the `MIN(year)` function.
   - The result includes `product_id` and the earliest year (`first_year`).

2. **Join with Sales Table:**
   - The `cte` is joined back with the `Sales` table using `product_id` and `year` to filter records for the first year of sales only.

3. **Select Required Columns:**
   - The `SELECT` statement retrieves the `product_id`, `first_year`, `quantity`, and `price` for each product's first year of sales.

4. **Final Result:**
   - The result contains one row per product corresponding to its first year of sales, along with the specified details.

---

#### Example Execution:

For the given input, the query will:
1. Compute the earliest year for each product (`MIN(year)`).
   - Product `100`: First year = `2008`
   - Product `200`: First year = `2011`
2. Join this result back with the `Sales` table to fetch `quantity` and `price` for these years.
3. Output the table as shown in the expected output.

---

#### Key Takeaways:
- **CTE Usage:** Simplifies breaking down a complex query into manageable parts.
- **Joins:** Enables combining results from multiple steps.
- **Aggregation:** Identifies key metrics such as the first year of sales effectively.
