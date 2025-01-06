# Finding the Topic of Each Post

## Table: Keywords
| Column Name | Type    |
|-------------|---------|
| topic_id    | int     |
| word        | varchar |

- `(topic_id, word)` is the primary key (combination of columns with unique values) for this table.
- Each row in this table contains the ID of a topic and a word that represents the topic.
- A topic can have multiple keywords, and a keyword can represent multiple topics.

## Table: Posts
| Column Name | Type    |
|-------------|---------|
| post_id     | int     |
| content     | varchar |

- `post_id` is the primary key (column with unique values) for this table.
- Each row in this table contains the ID of a post and its content.
- The content consists only of English letters and spaces.

## Task
LeetCode wants to identify the topics related to each post based on the following rules:
1. If a post contains a keyword from a topic (case-insensitive), it is associated with that topic.
2. If a post does not contain any keywords, its topic should be `"Ambiguous!"`.
3. If a post is associated with multiple topics, the topics should be listed in ascending order and separated by commas `','`.
4. The output should not have duplicate topic IDs.

Return the result in the specified format.

---

# Example

### Input

#### Keywords Table:
| topic_id | word     |
|----------|----------|
| 1        | handball |
| 1        | football |
| 3        | WAR      |
| 2        | Vaccine  |

#### Posts Table:
| post_id | content                                                                |
|---------|------------------------------------------------------------------------|
| 1       | We call it soccer They call it football hahaha                         |
| 2       | Americans prefer basketball while Europeans love handball and football |
| 3       | stop the war and play handball                                         |
| 4       | warning I planted some flowers this morning and then got vaccinated    |

### Output:
| post_id | topic      |
|---------|------------|
| 1       | 1          |
| 2       | 1          |
| 3       | 1,3        |
| 4       | Ambiguous! |

### Explanation:
1. Post 1: The word "football" matches topic 1.
2. Post 2: The words "handball" and "football" match topic 1.
3. Post 3: The words "war" and "handball" match topics 3 and 1, respectively.
4. Post 4: No keywords match; hence the topic is `"Ambiguous!"`.

---

# Thought Process

1. **Join the Tables**:
   - Use a `LEFT JOIN` between `Posts` and `Keywords` to check if any keyword matches the content of the post.

2. **Handle Case Insensitivity**:
   - Convert both the `content` and `word` columns to lowercase for comparison.

3. **Keyword Matching**:
   - Use the `LIKE` operator to check if a keyword exists in the content. Surround both the content and word with spaces to ensure accurate word matching (e.g., "war" doesn't match "warning").

4. **Group Results**:
   - Use `GROUP_CONCAT` to collect all matching `topic_id`s for each post.
   - Sort the `topic_id`s in ascending order and ensure no duplicates.

5. **Handle Ambiguous Posts**:
   - Use `IFNULL` to assign `"Ambiguous!"` if no keywords match.

6. **Group By Post**:
   - Group the results by `post_id` to ensure each post is processed independently.

---

# Solution

```sql
SELECT 
    p.post_id,
    IFNULL(
        GROUP_CONCAT(DISTINCT k.topic_id ORDER BY k.topic_id),
        'Ambiguous!'
    ) AS topic
FROM 
    Posts p
LEFT JOIN 
    Keywords k
ON 
    CONCAT(' ', LOWER(p.content), ' ') LIKE CONCAT('% ', LOWER(k.word), ' %')
GROUP BY 
    p.post_id;
```

---

# Solution Explanation

1. **Join Operation**:
   - The `LEFT JOIN` ensures all posts are included, even if they don't match any keywords.
   - The `LIKE` clause checks if a keyword exists in the post content.

2. **Case Insensitivity**:
   - `LOWER` is applied to both `content` and `word` for case-insensitive comparison.

3. **Exact Word Match**:
   - `CONCAT` adds spaces around both `content` and `word` to prevent partial matches.

4. **Aggregating Results**:
   - `GROUP_CONCAT` collects all unique `topic_id`s for each post, sorted in ascending order.

5. **Handle Ambiguity**:
   - `IFNULL` replaces `NULL` values (when no keywords match) with `"Ambiguous!"`.

6. **Grouping**:
   - `GROUP BY` ensures the result is grouped by each post.

---

# Key Takeaways
- Use `LEFT JOIN` to include unmatched rows.
- `CONCAT` with spaces helps match whole words.
- `GROUP_CONCAT` is useful for aggregating and sorting results.
- Always handle edge cases like missing matches using functions like `IFNULL`.

---

# Related Topics
- SQL Joins
- String Functions (`LOWER`, `CONCAT`, `LIKE`)
- Aggregation Functions (`GROUP_CONCAT`)
- Conditional Expressions (`IFNULL`)
