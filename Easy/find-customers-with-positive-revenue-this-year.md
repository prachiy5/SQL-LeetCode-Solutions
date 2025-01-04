# Find Customers With Positive Revenue this Year

### Problem Description
We have a table `Customers` that tracks customer IDs, the year, and their revenue. The task is to identify customers who had positive revenue in the year 2021. The result should return only the `customer_id` for these customers.

---

### Table Details

#### Customers Table:
| Column Name  | Type | Description                                    |
|--------------|------|------------------------------------------------|
| customer_id  | int  | Unique identifier for each customer            |
| year         | int  | Year associated with the revenue               |
| revenue      | int  | Revenue earned by the customer (can be negative)|

#### Notes:
- `(customer_id, year)` is the primary key.
- Revenue values can be negative.

---

### Task
Find the `customer_id` of customers who:
1. Had revenue greater than 0.
2. Earned that revenue in the year 2021.

---

### Input Example
**Customers Table:**
| customer_id | year | revenue |
|-------------|------|---------|
| 1           | 2018 | 50      |
| 1           | 2021 | 30      |
| 1           | 2020 | 70      |
| 2           | 2021 | -50     |
| 3           | 2018 | 10      |
| 3           | 2016 | 50      |
| 4           | 2021 | 20      |

---

### Output Example
**Result Table:**
| customer_id |
|-------------|
| 1           |
| 4           |

---

### Thought Process
1. Filter the rows where:
   - `year = 2021`.
   - `revenue > 0` (positive revenue).
2. Select only the `customer_id` column from the filtered results.

---

### Solution
```sql
SELECT customer_id 
FROM Customers
WHERE revenue > 0 AND year = 2021;
```

---

### Solution Explanation
1. **Filter Conditions:**
   - `revenue > 0`: Ensures we only include customers with positive revenue.
   - `year = 2021`: Limits results to the year 2021.
2. **Result Columns:**
   - `customer_id`: Only this column is selected since the task specifies returning customer IDs.
3. **Query Output:**
   - The query returns a list of customer IDs meeting the above criteria.

---

### Key Takeaways
1. Filtering using `WHERE` clause is effective for conditions involving specific values or ranges.
2. SQL queries can focus on specific columns to reduce unnecessary data in the result set.

---

### Related Topics
- Filtering rows with `WHERE`.
- Using logical operators (`AND`, `OR`).
