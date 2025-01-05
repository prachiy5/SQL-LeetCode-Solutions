# Count the Number of Experiments

#### Write a solution to report the number of experiments done on each of the three platforms for each of the three given experiments. Notice that all the pairs of (platform, experiment) should be included in the output including the pairs with zero experiments.
 **Schema:**

 Table: Experiments
```
 +---------------+----------+-----------------+
 | experiment_id | platform | experiment_name |
 +---------------+----------+-----------------+
 | 4             | IOS      | Programming     |
 | 13            | IOS      | Sports          |
 | 14            | Android  | Reading         |
 | 8             | Web      | Reading         |
 | 12            | Web      | Reading         |
 | 18            | Web      | Programming     |
 +---------------+----------+-----------------+
```
-- Example Output:
```
 +----------+-----------------+-----------------+
 | platform | experiment_name | num_experiments |
 +----------+-----------------+-----------------+
 | Android  | Reading         | 1               |
 | Android  | Sports          | 0               |
 | Android  | Programming     | 0               |
 | IOS      | Reading         | 0               |
 | IOS      | Sports          | 1               |
 | IOS      | Programming     | 1               |
 | Web      | Reading         | 2               |
 | Web      | Sports          | 0               |
 | Web      | Programming     | 1               |
 +----------+-----------------+-----------------+
```
 **Thought Process:**
1. We need to create a combination of all platforms (`Android`, `IOS`, `Web`) and all experiment names (`Reading`, `Sports`, `Programming`). This ensures that even zero occurrences are included.
2. Use a `WITH` clause to define the possible platforms and activities.
3. Perform a `LEFT JOIN` with the `Experiments` table to count the number of experiments for each pair of platform and experiment name.
4. Use conditional aggregation (`SUM` with a `CASE` statement) to count experiments.
5. Group the data by platform and experiment name to get the desired output.

 **Query:**
```sql
WITH platform AS (
    SELECT 'Android' AS platform
    UNION ALL
    SELECT 'IOS' AS platform
    UNION ALL
    SELECT 'Web' AS platform
),

activities AS (
    SELECT 'Reading' AS activity
    UNION ALL
    SELECT 'Sports' AS activity
    UNION ALL
    SELECT 'Programming' AS activity
)

SELECT 
    p.platform,
    e.activity AS experiment_name,
    SUM(CASE WHEN ep.experiment_name = e.activity THEN 1 ELSE 0 END) AS num_experiments
FROM 
    platform p
JOIN 
    activities e
LEFT JOIN 
    experiments ep 
ON 
    p.platform = ep.platform AND e.activity = ep.experiment_name
GROUP BY 
    p.platform, e.activity;
```
