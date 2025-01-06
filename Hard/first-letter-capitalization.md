### First Letter Capitalization

Table: user_content

| Column Name | Type    |
|-------------|---------|
| content_id  | int     |
| content_text| varchar |

- `content_id` is the unique key for this table.
- Each row contains a unique ID and the corresponding text content.

Write a solution to transform the text in the `content_text` column by applying the following rules:

1. Convert the first letter of each word to uppercase.
2. Keep all other letters in lowercase.
3. Preserve all existing spaces.

**Note:** There will be no special characters in `content_text`.

Return the result table that includes both the original `content_text` and the modified text where each word starts with a capital letter.

---

### Example:

#### Input:

`user_content` table:

| content_id | content_text                      |
|------------|-----------------------------------|
| 1          | hello world of SQL                |
| 2          | the QUICK brown fox               |
| 3          | data science AND machine learning |
| 4          | TOP rated programming BOOKS       |

#### Output:

| content_id | original_text                     | converted_text                    |
|------------|-----------------------------------|-----------------------------------|
| 1          | hello world of SQL                | Hello World Of Sql                |
| 2          | the QUICK brown fox               | The Quick Brown Fox               |
| 3          | data science AND machine learning | Data Science And Machine Learning |
| 4          | TOP rated programming BOOKS       | Top Rated Programming Books       |

#### Explanation:

1. **For `content_id = 1`:**
   - Each word's first letter is capitalized: `Hello World Of Sql`.

2. **For `content_id = 2`:**
   - Original mixed-case text is transformed to title case: `The Quick Brown Fox`.

3. **For `content_id = 3`:**
   - The word "AND" is converted to "And": `Data Science And Machine Learning`.

4. **For `content_id = 4`:**
   - Handles word "TOP rated" correctly: `Top Rated`.
   - Converts "BOOKS" from all caps to title case: `Books`.

---

### Thought Process:

1. **Understand the Task:**
   - Transform the `content_text` column into a title-case version where the first letter of every word is capitalized.

2. **Preserve Spaces:**
   - Ensure the transformation keeps the original spacing intact.

3. **SQL Approach:**
   - Use string functions to manipulate the text:
     - Break the text into words based on spaces.
     - Apply capitalization to the first letter of each word while converting the rest to lowercase.

4. **Implementation:**
   - Use the recursive application of `LOWER`, `UPPER`, and string concatenation functions to achieve the desired transformation.

5. **Output the Results:**
   - Include `content_id`, `content_text` as `original_text`, and the transformed text as `converted_text`.

---

### Query:

```sql
SELECT 
    content_id,
    content_text AS original_text,
    TRIM(
        REPLACE(
            CONCAT(
                UPPER(SUBSTRING_INDEX(content_text, ' ', 1)),
                ' ',
                LOWER(SUBSTRING_INDEX(content_text, ' ', -1))
            ),
            SUBSTRING_INDEX(content_text, ' ', 1),
            UPPER(LEFT(content_text))
        )
    ) AS converted_text
FROM user_content;
