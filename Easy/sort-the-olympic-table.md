### Sort the Olympic Table

**Table: Olympic**

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| country       | varchar |
| gold_medals   | int     |
| silver_medals | int     |
| bronze_medals | int     |
+---------------+---------+
```
- In SQL, `country` is the primary key for this table.
- Each row in this table shows a country name and the number of gold, silver, and bronze medals it won in the Olympic games.

The **Olympic** table is sorted according to the following rules:
1. The country with more **gold medals** comes first.
2. If there is a tie in the **gold medals**, the country with more **silver medals** comes first.
3. If there is a tie in the **silver medals**, the country with more **bronze medals** comes first.
4. If there is a tie in the **bronze medals**, the countries with the tie are sorted in **ascending order lexicographically**.

Write a solution to sort the Olympic table according to these rules.

The result format is shown in the following example.

---

### Example 1:

**Input:**  
**Olympic** table:

| country     | gold_medals | silver_medals | bronze_medals |
|-------------|-------------|---------------|---------------|
| China       | 10          | 10            | 20            |
| South Sudan | 0           | 0             | 1             |
| USA         | 10          | 10            | 20            |
| Israel      | 2           | 2             | 3             |
| Egypt       | 2           | 2             | 2             |

**Output:**  

| country     | gold_medals | silver_medals | bronze_medals |
|-------------|-------------|---------------|---------------|
| China       | 10          | 10            | 20            |
| USA         | 10          | 10            | 20            |
| Israel      | 2           | 2             | 3             |
| Egypt       | 2           | 2             | 2             |
| South Sudan | 0           | 0             | 1             |

**Explanation:**  
- The tie between **China** and **USA** is broken by their lexicographical names. Since "China" is lexicographically smaller than "USA", it comes first.
- **Israel** comes before **Egypt** because it has more bronze medals.

---

### Thought Process:

1. **Understand Sorting Rules:**  
   - Start sorting by **gold_medals** in descending order.
   - If there is a tie, sort by **silver_medals** in descending order.
   - If there is still a tie, sort by **bronze_medals** in descending order.
   - If all medal counts are the same, sort by **country** in ascending lexicographical order.

2. **SQL Implementation:**  
   - Use the `ORDER BY` clause to apply multiple sorting levels.
   - For descending order, specify `DESC` for medal counts.
   - By default, `ORDER BY` sorts strings lexicographically in ascending order, so this works for the **country** column.

---

### Query:

```sql
SELECT *
FROM Olympic
ORDER BY 
    gold_medals DESC,
    silver_medals DESC,
    bronze_medals DESC,
    country;
```

---

### Explanation of the Query:

1. **Base Table:**  
   - Start with the `Olympic` table.

2. **Sorting Rules:**  
   - Sort by `gold_medals` in descending order.
   - For ties in `gold_medals`, sort by `silver_medals` in descending order.
   - For ties in `silver_medals`, sort by `bronze_medals` in descending order.
   - For ties in `bronze_medals`, sort by `country` lexicographically (default ascending).

3. **Final Output:**  
   - The resulting table will display all countries sorted according to the given rules.

---

### Why this Output?
- Sorting ensures medals have higher priority over lexicographical order.
- Breaks ties appropriately at each level based on the sorting rules.
