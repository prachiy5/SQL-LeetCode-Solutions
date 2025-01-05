### Consecutive Available Seats II

#### Table: Cinema
| Column Name | Type |
|-------------|------|
| seat_id     | int  |
| free        | bool |

- `seat_id` is an auto-increment column.
- Each row indicates whether a seat is free (`1`) or occupied (`0`).

#### Task
Write a query to find the length of the longest consecutive sequence of available seats in the cinema.

#### Notes:
1. There will always be at most one longest consecutive sequence.
2. If multiple consecutive sequences have the same length, include all of them in the output.
3. The output should be ordered by `first_seat_id` in ascending order.

#### Return Format
| Column Name         | Type |
|---------------------|------|
| first_seat_id       | int  |
| last_seat_id        | int  |
| consecutive_seats_len | int |

---

### Example Input/Output

#### Input:
**Cinema Table:**
| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |

#### Output:
| first_seat_id | last_seat_id | consecutive_seats_len |
|---------------|--------------|-----------------------|
| 3             | 5            | 3                     |

#### Explanation:
The longest consecutive sequence of available seats starts from `seat_id` 3 and ends at `seat_id` 5 with a length of 3.
The output is ordered by `first_seat_id` in ascending order.

---

### Thought Process
1. **Filter Free Seats**: Identify only the rows where `free = 1`.
2. **Group Consecutive Seats**: Use a difference-based grouping technique (`row_number() - seat_id`) to assign a unique group to each sequence of consecutive free seats.
3. **Aggregate Group Information**:
   - Compute the `first_seat_id`, `last_seat_id`, and the length (`count(*)`) for each group.
4. **Identify Longest Sequence(s)**:
   - Rank the sequences by their length (`count(*)`) in descending order.
   - Filter the top-ranked sequences with `rank() = 1`.
5. **Order Results**: Order the results by `first_seat_id`.

---

### Query
```sql
WITH cte AS (
    SELECT 
        seat_id,
        seat_id - ROW_NUMBER() OVER (PARTITION BY free ORDER BY seat_id) AS rn
    FROM cinema
    WHERE free = 1
),

cte2 AS (
    SELECT 
        MIN(seat_id) AS first_seat_id, 
        MAX(seat_id) AS last_seat_id, 
        COUNT(*) AS consecutive_seats_len,
        RANK() OVER (ORDER BY COUNT(*) DESC) AS r
    FROM cte
    GROUP BY rn
)

SELECT 
    first_seat_id,
    last_seat_id,
    consecutive_seats_len
FROM cte2
WHERE r = 1;
