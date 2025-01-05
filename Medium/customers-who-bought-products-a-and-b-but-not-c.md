###  Customers Who Bought Products A and B but Not C
We are given two tables:

#### Table: `Customers`

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| customer_name | varchar |

- `customer_id` is the primary key.
- Each row represents a customer with their unique ID and name.

#### Table: `Orders`

| Column Name   | Type    |
|---------------|---------|
| order_id      | int     |
| customer_id   | int     |
| product_name  | varchar |

- `order_id` is the primary key.
- `customer_id` references the customer who bought the product `product_name`.

### Task
Write a SQL query to identify customers who:
1. Bought products "A" and "B".
2. Did not buy the product "C".

Return the `customer_id` and `customer_name` of such customers, ordered by `customer_id`.

### Example
#### Input:
**Customers table:**

| customer_id | customer_name |
|-------------|---------------|
| 1           | Daniel        |
| 2           | Diana         |
| 3           | Elizabeth     |
| 4           | Jhon          |

**Orders table:**

| order_id | customer_id | product_name |
|----------|-------------|--------------|
| 10       | 1           | A            |
| 20       | 1           | B            |
| 30       | 1           | D            |
| 40       | 1           | C            |
| 50       | 2           | A            |
| 60       | 3           | A            |
| 70       | 3           | B            |
| 80       | 3           | D            |
| 90       | 4           | C            |

#### Output:

| customer_id | customer_name |
|-------------|---------------|
| 3           | Elizabeth     |

#### Explanation:
- **Daniel (ID 1):** Bought "A", "B", and "C". Not eligible.
- **Diana (ID 2):** Bought "A" only. Not eligible.
- **Elizabeth (ID 3):** Bought "A" and "B", but not "C". Eligible.
- **Jhon (ID 4):** Bought "C". Not eligible.

---

### Thought Process
1. **Join the Tables:**
   - Use an `INNER JOIN` between `Customers` and `Orders` to associate customer names with their product purchases.

2. **Filter Conditions:**
   - Identify `customer_id`s who:
     - Bought product "A".
     - Bought product "B".
     - Did **not** buy product "C".

3. **Combine Conditions:**
   - Use `IN` subqueries to filter customers who meet the conditions.

4. **Remove Duplicates:**
   - Use `DISTINCT` to avoid duplicate rows.

5. **Order the Results:**
   - Sort the result by `customer_id`.

---

### SQL Query
```sql
WITH cte AS (
    SELECT c.customer_id, customer_name, order_id, product_name 
    FROM customers c
    INNER JOIN orders o 
    ON c.customer_id = o.customer_id
)

SELECT DISTINCT customer_id, customer_name 
FROM cte 
WHERE customer_id IN (SELECT customer_id FROM orders WHERE product_name = 'A')
  AND customer_id IN (SELECT customer_id FROM orders WHERE product_name = 'B')
  AND customer_id NOT IN (SELECT customer_id FROM orders WHERE product_name = 'C')
ORDER BY customer_id;
