### Rank Scores

**Table: Scores**

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| score       | decimal |
+-------------+---------+
```
- `id` is the primary key (column with unique values) for this table.
- Each row of this table contains the score of a game. Score is a floating-point value with two decimal places.

Write a solution to find the rank of the scores. The ranking should be calculated according to the following rules:

1. The scores should be ranked from the highest to the lowest.
2. If there is a tie between two scores, both should have the same ranking.
3. After a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no holes between ranks.

Return the result table ordered by score in descending order.

The result format is in the following example.

---

**Example 1:**

**Input:**

Scores table:

```
+----+-------+
| id | score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

**Output:**

```
+-------+------+
| score | rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

**Explanation:**
- The score `4.00` appears twice and is ranked as `1`.
- The score `3.85` appears once and is ranked as `2`.
- The score `3.65` appears twice and is ranked as `3`.
- The score `3.50` appears once and is ranked as `4`.

---

### Thought Process:

1. **Understand the Requirements:**
   - The scores need to be ranked in descending order.
   - If two scores are the same, they must share the same rank.
   - The rank should be consecutive without any gaps after ties.

2. **Use the `DENSE_RANK` Function:**
   - SQL provides the `DENSE_RANK` function, which assigns ranks based on the order of values while ensuring that ties share the same rank and subsequent ranks remain consecutive.

3. **Order by Score Descending:**
   - Use `ORDER BY score DESC` within the `DENSE_RANK` function to rank the scores from highest to lowest.

4. **Select the Required Columns:**
   - Select the `score` and its corresponding rank.

5. **Order the Output:**
   - Ensure the result table is ordered by `score` in descending order as specified.

---

### Query:

```sql
SELECT 
    score,
    DENSE_RANK() OVER(ORDER BY score DESC) AS rank
FROM 
    Scores;
```

---

### Explanation of the Query:

1. **Rank Calculation:**
   - The `DENSE_RANK()` function ranks the scores in descending order using `ORDER BY score DESC`.
   - Scores with the same value receive the same rank.

2. **Columns:**
   - The `score` column is directly included in the result.
   - The calculated `rank` is included as a derived column.

3. **Order of Results:**
   - The output is automatically ordered by the `score` in descending order since that is the ordering applied in the `DENSE_RANK()` function.

---

### Output Explanation:

Using the provided example:

- Scores `4.00` are ranked as `1`.
- Score `3.85` is ranked as `2`.
- Scores `3.65` are ranked as `3`.
- Score `3.50` is ranked as `4`.

This aligns with the requirements and provides the desired output format.
