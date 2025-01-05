### Find Expensive Cities

**Table: Listings**

| Column Name | Type    |
|-------------|---------|
| listing_id  | int     |
| city        | varchar |
| price       | int     |

- `listing_id` is a column of unique values for this table.
- This table contains `listing_id`, `city`, and `price`.

Write a solution to find cities where the average home prices exceed the national average home price.

Return the result table sorted by city in ascending order.

---

**Example 1:**

**Input:**  
Listings table:

| listing_id | city         | price   |
|------------|--------------|---------|
| 113        | LosAngeles   | 7560386 |
| 136        | SanFrancisco | 2380268 |
| 92         | Chicago      | 9833209 |
| 60         | Chicago      | 5147582 |
| 8          | Chicago      | 5274441 |
| 79         | SanFrancisco | 8372065 |
| 37         | Chicago      | 7939595 |
| 53         | LosAngeles   | 4965123 |
| 178        | SanFrancisco | 999207  |
| 51         | NewYork      | 5951718 |
| 121        | NewYork      | 2893760 |

**Output:**  

| city       |
|------------|
| Chicago    |
| LosAngeles |

**Explanation:**
- The national average home price is $6,122,059.45. Among the cities listed:
  - Chicago has an average price of $7,048,706.75
  - Los Angeles has an average price of $6,277,754.5
  - San Francisco has an average price of $3,900,513.33
  - New York has an average price of $4,422,739
- Only Chicago and Los Angeles have average home prices exceeding the national average. Therefore, these two cities are included in the output table. The output table is sorted in ascending order based on the city names.

---

### Thought Process:

1. **Understand the Task**: Identify cities with average home prices exceeding the national average home price. The national average is computed as the average of all home prices.
2. **Group Data by City**: Use the `GROUP BY` clause to calculate the average home price for each city.
3. **Filter Cities**: Use the `HAVING` clause to include only cities whose average home price exceeds the national average home price.
4. **Sort the Results**: Use the `ORDER BY` clause to sort the cities alphabetically.
5. **Return the Output**: The result table should only contain the `city` column.

---

### Query:

```sql
SELECT city 
FROM listings
GROUP BY city
HAVING AVG(price) > (SELECT AVG(price) FROM listings)
ORDER BY city;
```

---

### Explanation:

1. **Group by City**: The `GROUP BY city` groups all listings by their respective cities.
2. **Compute Average per City**: The `AVG(price)` function calculates the average home price for each city.
3. **Compute National Average**: The subquery `(SELECT AVG(price) FROM listings)` calculates the national average home price by taking the average of all prices in the `listings` table.
4. **Filter Cities**: The `HAVING` clause filters out cities whose average price is less than or equal to the national average.
5. **Order Cities Alphabetically**: The `ORDER BY city` ensures that the resulting cities are listed in ascending alphabetical order.

---

### Why This Query Works:

- The `GROUP BY` clause aggregates data by city, enabling us to compute average prices for each city.
- The subquery ensures the national average is correctly calculated from the entire dataset.
- The `HAVING` clause is used to filter grouped data based on a condition, which is essential in this case.
- The `ORDER BY` clause guarantees a clean and organized output.

---

**Output:**
For the given example, the query identifies Chicago and LosAngeles as the cities with average home prices above the national average.
