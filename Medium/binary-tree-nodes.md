### Binary Tree Nodes

We are given a table:

#### Table: Tree
| Column Name | Type |
|-------------|------|
| N           | int  |
| P           | int  |

- `N` is the column of unique values representing the value of a node in a Binary Tree.
- `P` represents the parent of the node `N`.

#### Task
Write a query to determine the type of each node in the Binary Tree. Each node can be classified as:
1. **Root**: If the node does not have a parent (`P IS NULL`).
2. **Leaf**: If the node does not have any children (i.e., `N` is not present in the `P` column).
3. **Inner**: If the node is neither a root nor a leaf.

The result should include:
- `N`: The value of the node.
- `Type`: The type of the node (`Root`, `Leaf`, or `Inner`).

The result table should be ordered by `N` in ascending order.

---

### Example Input/Output

##### Input:
**Tree table:**
| N | P    |
|---|------|
| 1 | 2    |
| 3 | 2    |
| 6 | 8    |
| 9 | 8    |
| 2 | 5    |
| 8 | 5    |
| 5 | null |

##### Output:
| N | Type  |
|---|-------|
| 1 | Leaf  |
| 2 | Inner |
| 3 | Leaf  |
| 5 | Root  |
| 6 | Leaf  |
| 8 | Inner |
| 9 | Leaf  |

##### Explanation:
- Node `5` is the root node because it has no parent (`P IS NULL`).
- Nodes `1`, `3`, `6`, and `9` are leaf nodes because they do not have any children (i.e., they do not appear in the `P` column).
- Nodes `2` and `8` are inner nodes because they have both a parent and at least one child.

---

### Thought Process
1. **Classify Nodes:**
   - **Root:** A node where `P IS NULL`.
   - **Leaf:** A node where `N` is not present in the `P` column.
   - **Inner:** Any node that is neither a root nor a leaf.
2. **Use Conditional Logic:**
   - Use a `CASE` statement to classify nodes based on the above conditions.
3. **Order Results:**
   - Order the result table by `N` in ascending order.

---

### Query
```sql
SELECT N,
       CASE 
           WHEN P IS NULL THEN 'Root'
           WHEN N IN (SELECT DISTINCT P FROM Tree) THEN 'Inner'
           ELSE 'Leaf' 
       END AS `Type`
FROM Tree
ORDER BY N;
```

---

### Key Takeaways
- **Subqueries for Classification:**
  - A subquery is used to identify nodes that appear in the `P` column (indicating they are parents to other nodes).
- **Conditional Logic:**
  - The `CASE` statement efficiently categorizes each node based on its parent and child relationships.
- **Ordering:**
  - Sorting by `N` ensures the result is presented in the required order.

---

### Related Topics
- Conditional Logic in SQL
- Subqueries
- SQL Ordering
