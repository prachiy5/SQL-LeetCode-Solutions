### Pizza Toppings Cost Analysis

We are given a table:

#### Table: Toppings
| Column Name  | Type    |
|--------------|---------|
| topping_name | varchar |
| cost         | decimal |

- `topping_name` is the primary key.
- Each row contains:
  - `topping_name`: The name of the topping.
  - `cost`: The cost of the topping.

#### Task
Write a query to calculate the total cost of all possible 3-topping pizza combinations from a given list of toppings. Ensure the following conditions are met:
1. Do not include pizzas where a topping is repeated.
2. Toppings must be listed in alphabetical order in the result.
3. The total cost must be rounded to 2 decimal places.
4. Return the result table ordered by:
   - `total_cost` in descending order.
   - `pizza` in ascending order.

The result should include:
- `pizza`: A combination of three toppings in alphabetical order.
- `total_cost`: The total cost of the three toppings.

---

### Example Input/Output

##### Input:
**Toppings table:**
| topping_name | cost |
|--------------|------|
| Pepperoni    | 0.50 |
| Sausage      | 0.70 |
| Chicken      | 0.55 |
| Extra Cheese | 0.40 |

##### Output:
| pizza                          | total_cost |
|--------------------------------|------------|
| Chicken,Pepperoni,Sausage      | 1.75       |
| Chicken,Extra Cheese,Sausage   | 1.65       |
| Extra Cheese,Pepperoni,Sausage | 1.60       |
| Chicken,Extra Cheese,Pepperoni | 1.45       |

##### Explanation:
- The possible 3-topping combinations are:
  1. **Chicken, Pepperoni, Sausage:**
     - Total cost = $0.55 + $0.50 + $0.70 = $1.75.
  2. **Chicken, Extra Cheese, Sausage:**
     - Total cost = $0.55 + $0.40 + $0.70 = $1.65.
  3. **Extra Cheese, Pepperoni, Sausage:**
     - Total cost = $0.40 + $0.50 + $0.70 = $1.60.
  4. **Chicken, Extra Cheese, Pepperoni:**
     - Total cost = $0.55 + $0.40 + $0.50 = $1.45.
- These combinations are listed in descending order of `total_cost` and ascending order of `pizza`.

---

### Thought Process
1. **Generate Unique Combinations:**
   - Use self-joins to generate all possible 3-topping combinations.
   - Ensure each topping is unique and ordered alphabetically by adding conditions on the join.
2. **Calculate Total Cost:**
   - Sum the `cost` values of the three toppings for each combination.
3. **Order Results:**
   - Order by `total_cost` in descending order and `pizza` in ascending order.
4. **Format Output:**
   - Combine the topping names into a single string, separated by commas.

---

### Query
```sql
WITH top AS (
    SELECT *, ROW_NUMBER() OVER(ORDER BY topping_name ASC) AS rn
    FROM Toppings
)

SELECT CONCAT(t1.topping_name, ',', t2.topping_name, ',', t3.topping_name) AS pizza, 
       ROUND((t1.cost + t2.cost + t3.cost), 2) AS total_cost 
FROM top AS t1 
JOIN top AS t2 ON t1.topping_name != t2.topping_name AND t1.rn < t2.rn
JOIN top AS t3 ON t1.topping_name != t3.topping_name 
               AND t2.topping_name != t3.topping_name AND t2.rn < t3.rn
ORDER BY total_cost DESC, pizza ASC;
```

---

### Key Takeaways
- **Self-Joins for Combinations:**
  - Use self-joins to create all unique combinations of three toppings.
  - Ensure no duplicate or unordered combinations by using conditions (`rn` and `topping_name` comparisons).
- **String Concatenation:**
  - Combine topping names in alphabetical order using `CONCAT()`.
- **Ordering:**
  - Sort results by `total_cost` (descending) and `pizza` (ascending).
- **Rounding:**
  - Use `ROUND()` to round the total cost to two decimal places.

---

### Related Topics
- SQL Joins
- Window Functions
- String Manipulation in SQL
