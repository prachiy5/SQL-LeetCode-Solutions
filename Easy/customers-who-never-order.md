# Find Customers Who Never Ordered Anything

### Problem

#### Table: Customers

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key (column with unique values) for this table.
- Each row of this table indicates the ID and name of a customer.

#### Table: Orders

| Column Name | Type |
|-------------|------|
| id          | int  |
| customerId  | int  |

- `id` is the primary key (column with unique values) for this table.
- `customerId` is a foreign key referencing the `id` column in the `Customers` table.
- Each row of this table indicates the ID of an order and the ID of the customer who placed it.

#### Task
Write a solution to find all customers who never ordered anything.

Return the result table in any order.

#### Example

**Input:**

`Customers` table:

| id | name  |
|----|-------|
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |

`Orders` table:

| id | customerId |
|----|------------|
| 1  | 3          |
| 2  | 1          |

**Output:**

| Customers |
|-----------|
| Henry     |
| Max       |

**Explanation:**
- Joe and Sam have orders in the `Orders` table, so they are excluded.
- Henry and Max have no orders, so they are included in the output.

---

### Thought Process

To solve this problem, the goal is to find customers who are not present in the `Orders` table. Here’s how I approached it step by step:

1. **Understand the Relationship:**
   - The `Orders` table contains a `customerId` column that links to the `id` column in the `Customers` table.
   - If a customer's `id` is not present in the `Orders` table, it means they have not placed any orders.

2. **Identify Customers Without Orders:**
   - Use a subquery to get all `customerId` values from the `Orders` table.
   - Compare the `id` of each customer in the `Customers` table against this list.

3. **Filter Using `NOT IN`:**
   - Use the `NOT IN` clause to exclude customers who have `id` values in the list of `customerId` values from the `Orders` table.

4. **Select the Customer Name:**
   - Return only the `name` column of customers who meet the condition.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT name AS 'Customers'
FROM Customers c
WHERE id NOT IN (SELECT customerId FROM Orders);
```

---

### Solution Explanation

- **Subquery:**
  - The subquery `SELECT customerId FROM Orders` gets all `customerId` values from the `Orders` table.
- **NOT IN Clause:**
  - Filters out customers whose `id` is in the list of `customerId` values, leaving only those who never placed an order.
- **Select Statement:**
  - Retrieves the `name` of customers who meet the condition and renames the output column as `Customers`.

---

### Key Takeaways

- **Subqueries:** Useful for filtering results based on another query.
- **NOT IN Clause:** Ideal for finding records not present in a specific list.
- **Handling Relationships:** Understanding primary and foreign keys is essential for solving such problems.

---

### Related Topics
- Subqueries
- Filtering with NOT IN
- Primary and Foreign Key Relationships
