### Count Artist Occurrences On Spotify Ranking List

Table: Spotify

```plaintext
+-------------+---------+ 
| Column Name | Type    | 
+-------------+---------+ 
| id          | int     | 
| track_name  | varchar |
| artist      | varchar |
+-------------+---------+
```

`id` is the primary key (column with unique values) for this table.
Each row contains an `id`, `track_name`, and `artist`.

Write a solution to find how many times each artist appeared on the Spotify ranking list.

Return the result table having the artist's name along with the corresponding number of occurrences ordered by occurrence count in descending order. If the occurrences are equal, then it’s ordered by the artist’s name in ascending order.

The result format is in the following example.

#### Example 1:

**Input:**
Spotify table: 

```plaintext
+---------+--------------------+------------+ 
| id      | track_name         | artist     |  
+---------+--------------------+------------+
| 303651  | Heart Won't Forget | Sia        |
| 1046089 | Shape of you       | Ed Sheeran |
| 33445   | I'm the one        | DJ Khalid  |
| 811266  | Young Dumb & Broke | DJ Khalid  | 
| 505727  | Happier            | Ed Sheeran |
+---------+--------------------+------------+ 
```

**Output:**

```plaintext
+------------+-------------+
| artist     | occurrences | 
+------------+-------------+
| DJ Khalid  | 2           |
| Ed Sheeran | 2           |
| Sia        | 1           | 
+------------+-------------+ 
```

**Explanation:** The count of occurrences is listed in descending order under the column name "occurrences". If the number of occurrences is the same, the artist's names are sorted in ascending order.

---

### Thought Process:

1. **Understand the Requirement:**
   - The task is to count how many times each artist appears in the `Spotify` table.
   - The result should include each artist's name and their count of occurrences.
   - The results must be ordered by:
     - Occurrences in descending order.
     - If occurrences are the same, sort by the artist's name in ascending order.

2. **Key Points:**
   - Use `GROUP BY` to group rows by the artist.
   - Use `COUNT` to count the number of rows for each artist.
   - Use `ORDER BY` for sorting based on the requirements.

3. **Approach:**
   - Group the table by the `artist` column.
   - Count the occurrences of each artist using `COUNT(artist)`.
   - Order the result by:
     - Occurrence count (`COUNT(artist)`) in descending order.
     - Artist name (`artist`) in ascending order.

4. **Output Format:**
   - Return two columns:
     - `artist`
     - `occurrences`

---

### Query:

```sql
SELECT 
    artist, 
    COUNT(artist) AS occurrences
FROM 
    spotify
GROUP BY 
    artist
ORDER BY 
    occurrences DESC, 
    artist ASC;
```

---

### Explanation of the Query:

1. **Count Occurrences:**
   - The `COUNT(artist)` function counts how many times each artist appears in the table.

2. **Group by Artist:**
   - The `GROUP BY artist` groups all rows by the `artist` column.

3. **Sort the Results:**
   - `ORDER BY occurrences DESC` ensures that artists with higher counts appear first.
   - `ORDER BY artist ASC` ensures that artists with the same count are sorted alphabetically.

---

### Why this Output?

Given the input data:

| id      | track_name         | artist     |
|---------|--------------------|------------|
| 303651  | Heart Won't Forget | Sia        |
| 1046089 | Shape of you       | Ed Sheeran |
| 33445   | I'm the one        | DJ Khalid  |
| 811266  | Young Dumb & Broke | DJ Khalid  |
| 505727  | Happier            | Ed Sheeran |

- DJ Khalid appears **2 times**.
- Ed Sheeran appears **2 times**.
- Sia appears **1 time**.

After sorting by occurrences (descending) and artist name (ascending):

| artist     | occurrences |
|------------|-------------|
| DJ Khalid  | 2           |
| Ed Sheeran | 2           |
| Sia        | 1           |

---
