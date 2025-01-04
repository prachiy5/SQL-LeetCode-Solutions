# Combine Two Tables

### Problem

#### Table: Person

| Column Name | Type    |
|-------------|---------|
| personId    | int     |
| lastName    | varchar |
| firstName   | varchar |

- `personId` is the primary key (column with unique values) for this table.
- This table contains information about the ID of some persons and their first and last names.

#### Table: Address

| Column Name | Type    |
|-------------|---------|
| addressId   | int     |
| personId    | int     |
| city        | varchar |
| state       | varchar |

- `addressId` is the primary key (column with unique values) for this table.
- Each row of this table contains information about the city and state of one person with ID = `personId`.

#### Task
Write a solution to report the first name, last name, city, and state of each person in the `Person` table. If the address of a `personId` is not present in the `Address` table, report `null` instead.

Return the result table in any order.

#### Example

**Input:**

`Person` table:

| personId | lastName | firstName |
|----------|----------|-----------|
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |

`Address` table:

| addressId | personId | city          | state      |
|-----------|----------|---------------|------------|
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |

**Output:**

| firstName | lastName | city          | state    |
|-----------|----------|---------------|----------|
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |

**Explanation:**
- There is no address in the `Address` table for the `personId = 1` so we return `null` in their `city` and `state`.
- `addressId = 1` contains information about the address of `personId = 2`.

---

### Thought Process

To solve this problem, the goal is to combine data from two tables: `Person` and `Address`. Here’s how I approached the problem step by step:

1. **Understand the Relationships:**
   - The `personId` column connects both tables.
   - We need to ensure every person in the `Person` table is included, even if they don’t have a matching address.

2. **Choose the Right Join:**
   - A **LEFT JOIN** is needed because it includes all rows from the `Person` table and matches rows from the `Address` table. If no match is found, `NULL` will be returned for the `Address` columns.

3. **Select Required Columns:**
   - Retrieve `firstName` and `lastName` from the `Person` table.
   - Retrieve `city` and `state` from the `Address` table.

4. **Write the Query:**
   - Use `LEFT JOIN` to combine the tables on the `personId` column.
   - Select the desired columns in the final output.

---

### Solution

```sql
# Write your MySQL query statement below
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId;
```

---

### Key Takeaways

- **LEFT JOIN** is used when you want all rows from the left table (`Person`) and matching rows from the right table (`Address`). If no match is found, `NULL` is returned for the right table columns.
- Ensures no data is missed from the `Person` table, even if there are no addresses available.

---

### Related Topics
- Joins (LEFT JOIN, INNER JOIN, etc.)
- Primary and Foreign Keys
- Handling NULL values in SQL
