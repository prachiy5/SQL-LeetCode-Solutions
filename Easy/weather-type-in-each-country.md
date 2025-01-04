#  Weather Type in Each Country 

## Problem Description
We are tasked to determine the type of weather for each country for November 2019 based on the `weather_state` values:

- **Cold**: Average `weather_state` <= 15
- **Hot**: Average `weather_state` >= 25
- **Warm**: Otherwise

The `Countries` table provides information about countries, and the `Weather` table provides weather data for specific days. We need to calculate the average `weather_state` for November 2019 for each country and classify the weather type accordingly.

## Input Tables
### `Countries` Table
| country_id | country_name |
|------------|--------------|
| 2          | USA          |
| 3          | Australia    |
| 7          | Peru         |
| 5          | China        |
| 8          | Morocco      |
| 9          | Spain        |

### `Weather` Table
| country_id | weather_state | day        |
|------------|---------------|------------|
| 2          | 15            | 2019-11-01 |
| 2          | 12            | 2019-10-28 |
| 3          | -2            | 2019-11-10 |
| 5          | 16            | 2019-11-07 |
| 8          | 25            | 2019-11-05 |
| 9          | 7             | 2019-10-23 |

## Output Table
| country_name | weather_type |
|--------------|--------------|
| USA          | Cold         |
| Australia    | Cold         |
| Peru         | Hot          |
| Morocco      | Hot          |
| China        | Warm         |

## SQL Solution
```sql
SELECT country_name,
       CASE 
           WHEN AVG(weather_state) <= 15 THEN 'Cold'
           WHEN AVG(weather_state) >= 25 THEN 'Hot'
           ELSE 'Warm'
       END AS weather_type
FROM Countries c
INNER JOIN Weather w
ON c.country_id = w.country_id
WHERE DATE_FORMAT(day, '%Y-%m') = '2019-11'
GROUP BY country_name;
```

## Thought Process and Approach
1. **Understand the Problem**:
   - Identify which columns are needed from the `Countries` and `Weather` tables.
   - Determine how to calculate the average `weather_state` for each country in November 2019.

2. **Filter the Data**:
   - Use `DATE_FORMAT(day, '%Y-%m') = '2019-11'` to restrict the data to November 2019.

3. **Join the Tables**:
   - Perform an `INNER JOIN` on the `country_id` column to link the `Countries` and `Weather` tables.

4. **Calculate the Average**:
   - Group by `country_name` to calculate the average `weather_state` for each country.

5. **Classify Weather**:
   - Use a `CASE` statement to assign the appropriate weather type based on the average value.

6. **Return the Results**:
   - Select `country_name` and the calculated `weather_type`, ensuring the output matches the expected format.

## Key Notes
- The query uses `AVG()` to calculate the average `weather_state` for each country.
- The `CASE` statement handles the classification of weather into "Cold," "Hot," or "Warm."
- The `GROUP BY` clause ensures that the average is calculated for each country.
- Countries with no data for November 2019 are excluded from the result automatically due to the `INNER JOIN`.

## Example Walkthrough
For the country `USA`:
- Weather states for November 2019: `[15]`
- Average = `15`
- Weather type = `Cold`

For the country `Morocco`:
- Weather states for November 2019: `[25, 27, 31]`
- Average = `(25 + 27 + 31) / 3 = 27.67`
- Weather type = `Hot`

## Conclusion
This query calculates the weather type for each country effectively, using filtering, grouping, and conditional logic. The result matches the expected output format.
