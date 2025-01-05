### Problem Description

We are given a table:

#### Table: Fraud
| Column Name | Type    |
|-------------|---------|
| policy_id   | int     |
| state       | varchar |
| fraud_score | int     |

- `policy_id` is the primary key.
- Each row contains:
  - `policy_id`: Unique identifier for the policy.
  - `state`: The state where the policy belongs.
  - `fraud_score`: The fraud score assigned to the policy by the predictive model.

#### Task
Write a query to identify the top 5 percentile of claims from each state, as flagged by the fraud detection model. The results should meet the following requirements:
1. Include only policies within the top 5% of fraud scores for each state.
2. Order the result by:
   - `state` in ascending order.
   - `fraud_score` in descending order.
   - `policy_id` in ascending order.

---

### Example Input/Output

##### Input:
**Fraud table:**
| policy_id | state      | fraud_score |
|-----------|------------|-------------|
| 1         | California | 0.92        |
| 2         | California | 0.68        |
| 3         | California | 0.17        |
| 4         | New York   | 0.94        |
| 5         | New York   | 0.81        |
| 6         | New York   | 0.77        |
| 7         | Texas      | 0.98        |
| 8         | Texas      | 0.97        |
| 9         | Texas      | 0.96        |
| 10        | Florida    | 0.97        |
| 11        | Florida    | 0.98        |
| 12        | Florida    | 0.78        |
| 13        | Florida    | 0.88        |
| 14        | Florida    | 0.66        |

##### Output:
| policy_id | state      | fraud_score |
|-----------|------------|-------------|
| 1         | California | 0.92        |
| 11        | Florida    | 0.98        |
| 4         | New York   | 0.94        |
| 7         | Texas      | 0.98        |

##### Explanation:
- **California:** Policy ID 1 (fraud score 0.92) falls in the top 5 percentile.
- **Florida:** Policy ID 11 (fraud score 0.98) is the top 5 percentile.
- **New York:** Policy ID 4 (fraud score 0.94) is the top 5 percentile.
- **Texas:** Policy ID 7 (fraud score 0.98) is the top 5 percentile.

---

### Thought Process
1. **Calculate Percentile Rank:**
   - Use the `PERCENT_RANK()` window function to assign a rank to each policy within its state based on its `fraud_score`.
   - Order by `fraud_score` in descending order within each state.
2. **Filter Top 5%:**
   - Include only rows where the percentile rank is less than or equal to 0.05 (top 5 percentile).
3. **Sort the Result:**
   - Order the output by:
     - `state` in ascending order.
     - `fraud_score` in descending order.
     - `policy_id` in ascending order.

---

### Query
```sql
WITH cte AS (
    SELECT *,
           PERCENT_RANK() OVER(PARTITION BY state ORDER BY fraud_score DESC) AS pr
    FROM fraud
)

SELECT policy_id, state, fraud_score
FROM cte
WHERE pr <= 0.05
ORDER BY state, fraud_score DESC, policy_id;
```

---

### Key Takeaways
- **Percentile Calculation:**
  - The `PERCENT_RANK()` function is used to calculate the rank of each policy's fraud score within its state.
- **Filtering by Percentile:**
  - The top 5% is determined by filtering rows where the percentile rank (`pr`) is less than or equal to 0.05.
- **Ordering Results:**
  - The results are ordered to meet the specified requirements: `state` ascending, `fraud_score` descending, and `policy_id` ascending.

---

### Related Topics
- SQL Window Functions
- Filtering with Percentiles
- SQL Ordering and Grouping
