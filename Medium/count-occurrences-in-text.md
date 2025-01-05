### Count Occurrences in Text
We are given a table:

#### Table: Files
| Column Name | Type    |
|-------------|---------|
| file_name   | varchar |
| content     | text    |

- `file_name` is the column with unique values.
- Each row contains:
  - `file_name`: The name of the file.
  - `content`: The textual content of the file.

#### Task
Write a query to count the number of files that contain at least one occurrence of the words "bull" and "bear" as standalone words, respectively. The words must appear as standalone words (i.e., they should have a space on both sides and should not be part of another word like "bullet" or "bears").

Return the word (`bull` or `bear`) along with its corresponding count in any order.

---

### Example Input/Output

##### Input:
**Files table:**
| file_name  | content                                                                         |
|------------|---------------------------------------------------------------------------------|
| draft1.txt | The stock exchange predicts a bull market which would make many investors happy.|
| draft2.txt | The stock exchange predicts a bull market which would make many investors happy,|
|            | but analysts warn of possibility of too much optimism and that in fact we are   |
|            | awaiting a bear market.                                                        |
| draft3.txt | The stock exchange predicts a bull market which would make many investors happy,|
|            | but analysts warn of possibility of too much optimism and that in fact we are   |
|            | awaiting a bear market. As always predicting the future market is an uncertain  |
|            | game and all investors should follow their instincts and best practices.        |

##### Output:
| word | count |
|------|-------|
| bull | 3     |
| bear | 2     |

##### Explanation:
- The word **"bull"** appears:
  - 1 time in `draft1.txt`.
  - 1 time in `draft2.txt`.
  - 1 time in `draft3.txt`.
  - Total: `3` occurrences.

- The word **"bear"** appears:
  - 1 time in `draft2.txt`.
  - 1 time in `draft3.txt`.
  - Total: `2` occurrences.

---

### Thought Process
1. **Identify Standalone Words:**
   - Use the `LOCATE()` function to find occurrences of " bull " and " bear " (with spaces on both sides) in the `content` column.
   - This ensures only standalone occurrences are counted.

2. **Count Occurrences:**
   - Use `SUM()` with a `CASE` statement to count the number of files containing "bull" or "bear".
   - Check for the presence of " bull " and " bear " separately.

3. **Combine Results:**
   - Use a `UNION ALL` query to combine the counts for "bull" and "bear" into a single result table.

4. **Return Results:**
   - Return the word (`bull` or `bear`) and its corresponding count.

---

### Query
```sql
SELECT 'bull' AS word,
       SUM(CASE WHEN LOCATE(' bull ', content) > 0 THEN 1 ELSE 0 END) AS count
FROM files
UNION ALL
SELECT 'bear' AS word,
       SUM(CASE WHEN LOCATE(' bear ', content) > 0 THEN 1 ELSE 0 END) AS count
FROM files;
```

---

### Key Takeaways
- **Standalone Words:** Adding spaces before and after the target words ensures that we only count standalone occurrences.
- **Conditional Aggregation:** The `CASE` statement inside `SUM()` allows us to count occurrences based on a condition.
- **Combining Results:** Using `UNION ALL` combines the counts for different words into a single result set.

---

### Related Topics
- SQL String Functions (`LOCATE`)
- Conditional Logic in SQL (`CASE`)
- Aggregate Functions (`SUM`)
- Combining Results (`UNION ALL`)

