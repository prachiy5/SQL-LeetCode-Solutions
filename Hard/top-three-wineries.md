### Top Three Wineries 

Table: Wineries

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| country     | varchar |
| points      | int     |
| winery      | varchar |

- `id` is a column of unique values for this table.
- This table contains `id`, `country`, `points`, and `winery`.

Write a solution to find the top three wineries in each country based on their total points. If multiple wineries have the same total points, order them by winery name in ascending order. If there's no second winery, output 'No second winery,' and if there's no third winery, output 'No third winery.'

Return the result table ordered by `country` in ascending order.

---

### Example:

#### Input:

Wineries table:

| id  | country   | points | winery          |
|-----|-----------|--------|-----------------|
| 103 | Australia | 84     | WhisperingPines |
| 737 | Australia | 85     | GrapesGalore    |
| 848 | Australia | 100    | HarmonyHill     |
| 222 | Hungary   | 60     | MoonlitCellars  |
| 116 | USA       | 47     | RoyalVines      |
| 124 | USA       | 45     | Eagle'sNest     |
| 648 | India     | 69     | SunsetVines     |
| 894 | USA       | 39     | RoyalVines      |
| 677 | USA       | 9      | PacificCrest    |

#### Output:

| country   | top_winery          | second_winery     | third_winery         |
|-----------|---------------------|-------------------|----------------------|
| Australia | HarmonyHill (100)   | GrapesGalore (85) | WhisperingPines (84) |
| Hungary   | MoonlitCellars (60) | No second winery  | No third winery      |
| India     | SunsetVines (69)    | No second winery  | No third winery      |
| USA       | RoyalVines (86)     | Eagle'sNest (45)  | PacificCrest (9)     |

#### Explanation:
- **Australia**: HarmonyHill accumulates the highest score (100 points). GrapesGalore secures second (85 points). WhisperingPines ranks third (84 points).
- **Hungary**: MoonlitCellars is the only winery, making it the top winery with no second or third.
- **India**: SunsetVines is the sole winery, earning the top position with no second or third.
- **USA**: RoyalVines accumulates 86 points (sum of 47 and 39), followed by Eagle'sNest (45 points) and PacificCrest (9 points).

---

### Thought Process:
1. **Aggregate Points**: Use `SUM(points)` to calculate total points for each winery grouped by `country` and `winery`.
2. **Rank Wineries**: Use `DENSE_RANK()` to assign ranks to wineries within each country based on their total points, with ties resolved by winery name in ascending order.
3. **Handle Missing Rankings**: Use `COALESCE()` to replace missing rankings with default messages ('No second winery', 'No third winery').
4. **Format Output**: Combine the winery name and total points into the format `WineryName (Points)`.
5. **Sort Results**: Order the results by `country`.

---

### Query:

```sql
WITH cte AS (
    SELECT country, winery, SUM(points) AS total_points
    FROM Wineries
    GROUP BY country, winery
),
cte2 AS (
    SELECT 
        country, 
        CONCAT(winery, ' (', total_points, ')') AS winery,
        DENSE_RANK() OVER (PARTITION BY country ORDER BY total_points DESC, winery ASC) AS rn
    FROM cte
)

SELECT 
    country,
    COALESCE(MAX(CASE WHEN rn = 1 THEN winery END), 'No top winery') AS top_winery,
    COALESCE(MAX(CASE WHEN rn = 2 THEN winery END), 'No second winery') AS second_winery,
    COALESCE(MAX(CASE WHEN rn = 3 THEN winery END), 'No third winery') AS third_winery
FROM 
    cte2
GROUP BY 
    country
ORDER BY 
    country;
