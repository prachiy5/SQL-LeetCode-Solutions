### Tree Node:

**Table:** Tree

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| p_id        | int  |
+-------------+------+
```
- `id` is the column with unique values for this table.
- Each row of this table contains information about the `id` of a node and the `id` of its parent node in a tree.
- The given structure is always a valid tree.

Each node in the tree can be one of three types:

- **"Leaf"**: if the node is a leaf node.
- **"Root"**: if the node is the root of the tree.
- **"Inner"**: if the node is neither a leaf node nor a root node.

Write a solution to report the type of each node in the tree.

**Return** the result table in any order.

### Example 1:

**Input:**

Tree table:

```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

**Output:**

```
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
| 2  | Inner |
| 3  | Leaf  |
| 4  | Leaf  |
| 5  | Leaf  |
+----+-------+
```

**Explanation:**
- Node `1` is the root node because its parent node is `null` and it has child nodes `2` and `3`.
- Node `2` is an inner node because it has parent node `1` and child nodes `4` and `5`.
- Nodes `3`, `4`, and `5` are leaf nodes because they have parent nodes and they do not have child nodes.

### Example 2:

**Input:**

Tree table:

```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
+----+------+
```

**Output:**

```
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
+----+-------+
```

**Explanation:**
If there is only one node in the tree, you only need to output its root attributes.

---

### Thought Process:

1. **Identify the Root Node:**
   - A root node is a node where `p_id` is `null`.

2. **Identify Leaf Nodes:**
   - A leaf node is a node that does not appear as a `p_id` in the table, meaning it has no children.

3. **Identify Inner Nodes:**
   - An inner node is a node that is neither a root nor a leaf. It has a `p_id` (indicating it has a parent) and also appears as a `p_id` for other nodes (indicating it has children).

4. **Case Statement:**
   - Use a `CASE` statement to classify each node based on the above criteria.

5. **Result Table:**
   - Return `id` and its respective `type`.

---

### Query:

```sql
SELECT id,
       CASE
           WHEN p_id IS NULL THEN 'Root'
           WHEN id NOT IN (SELECT p_id FROM tree WHERE p_id IS NOT NULL) THEN 'Leaf'
           ELSE 'Inner'
       END AS type
FROM tree;
```

---

### Explanation of the Query:

1. **Root Identification:**
   - `WHEN p_id IS NULL THEN 'Root'`: Checks if the `p_id` is `null`, marking the node as a root.

2. **Leaf Identification:**
   - `WHEN id NOT IN (SELECT p_id FROM tree WHERE p_id IS NOT NULL) THEN 'Leaf'`: Checks if the `id` is not found as a `p_id` in the table, meaning the node has no children.

3. **Inner Identification:**
   - `ELSE 'Inner'`: If the node is neither root nor leaf, it is classified as an inner node.

4. **Final Result:**
   - Returns each `id` along with its computed `type`.

---

### Output:

For the provided examples, the query will return:

**Example 1 Output:**

```
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
| 2  | Inner |
| 3  | Leaf  |
| 4  | Leaf  |
| 5  | Leaf  |
+----+-------+
```

**Example 2 Output:**

```
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
+----+-------+
