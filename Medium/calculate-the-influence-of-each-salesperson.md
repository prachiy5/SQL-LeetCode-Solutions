### Calculate the Influence of Each Salesperson
We are given three tables:

#### Table: Salesperson
| Column Name    | Type    |
|----------------|---------|
| salesperson_id | int     |
| name           | varchar |

- `salesperson_id` contains unique values.
- Each row in this table shows the ID of a salesperson.

#### Table: Customer
| Column Name    | Type |
|----------------|------|
| customer_id    | int  |
| salesperson_id | int  |

- `customer_id` contains unique values.
- `salesperson_id` is a foreign key referencing the `Salesperson` table.
- Each row in this table shows the ID of a customer and their associated salesperson.

#### Table: Sales
| Column Name | Type |
|-------------|------|
| sale_id     | int  |
| customer_id | int  |
| price       | int  |

- `sale_id` contains unique values.
- `customer_id` is a foreign key referencing the `Customer` table.
- Each row in this table shows the ID of a customer and the price they paid for the sale.

#### Task
Write a query to report the sum of prices paid by the customers of each salesperson. If a salesperson does not have any customers, the total value should be `0`.

The result should contain the following columns:
- `salesperson_id`
- `name`
- `total` (sum of prices paid by their customers, or `0` if no customers are associated)

The result can be returned in any order.

#### Example Input/Output

##### Input:
**Salesperson table:**
| salesperson_id | name  |
|----------------|-------|
| 1              | Alice |
| 2              | Bob   |
| 3              | Jerry |

**Customer table:**
| customer_id | salesperson_id |
|-------------|----------------|
| 1           | 1              |
| 2           | 1              |
| 3           | 2              |

**Sales table:**
| sale_id | customer_id | price |
|---------|-------------|-------|
| 1       | 2           | 892   |
| 2       | 1           | 354   |
| 3       | 3           | 988   |
| 4       | 3           | 856   |

##### Output:
| salesperson_id | name  | total |
|----------------|-------|-------|
| 1              | Alice | 1246  |
| 2              | Bob   | 1844  |
| 3              | Jerry | 0     |

##### Explanation:
- **Alice:**
  - Customer 1 made one purchase for `354`.
  - Customer 2 made one purchase for `892`.
  - Total = `354 + 892 = 1246`.

- **Bob:**
  - Customer 3 made two purchases for `988` and `856`.
  - Total = `988 + 856 = 1844`.

- **Jerry:**
  - No customers are associated with Jerry.
  - Total = `0`.

---

### Thought Process
1. Start with the `Salesperson` table since we need to report totals for all salespeople, even if they have no customers.
2. Perform a `LEFT JOIN` between `Salesperson` and `Customer` to include all salespeople, even those without associated customers.
3. Perform another `LEFT JOIN` with the `Sales` table to calculate the total price for each salesperson's customers.
4. Use `SUM(price)` to compute the total price and handle null values using `IFNULL()` to ensure `0` is returned for salespeople without customers.
5. Group the results by `salesperson_id` to calculate the total for each salesperson.

---

### Query
```sql
SELECT sp.salesperson_id, sp.name, IFNULL(SUM(price), 0) AS total
FROM Salesperson sp
LEFT JOIN customer c
ON sp.salesperson_id = c.salesperson_id
LEFT JOIN sales s
ON c.customer_id = s.customer_id
GROUP BY salesperson_id;
```

---

### Key Takeaways
- **LEFT JOIN:** Ensures that all salespeople are included, even if they do not have customers or sales.
- **Aggregate Functions:** Using `SUM()` calculates the total sales price for each salesperson's customers.
- **NULL Handling:** The `IFNULL()` function is used to replace `NULL` values with `0` for salespeople without any customers or sales.
- **Grouping:** Grouping by `salesperson_id` ensures we calculate totals for each salesperson individually.

---

### Related Topics
- SQL Joins
- Aggregate Functions
- Null Handling in SQL
- Grouping and Aggregation
