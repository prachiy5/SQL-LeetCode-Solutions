# Daily Leads and Partners

### Problem

#### Table: DailySales

| Column Name | Type    |
|-------------|---------|
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |

- This table contains the sales data for each product (`make_name`) on a given date (`date_id`).
- `lead_id` and `partner_id` represent unique identifiers for the lead and partner involved in the sale.
- There is no primary key, so the table may contain duplicate rows.

#### Task

For each `date_id` and `make_name`, calculate:

1. The number of **distinct** `lead_id`s (unique leads).
2. The number of **distinct** `partner_id`s (unique partners).

Return the results in any order.

---

### Example

#### Input

**DailySales Table**:

| date_id   | make_name | lead_id | partner_id |
|-----------|-----------|---------|------------|
| 2020-12-8 | toyota    | 0       | 1          |
| 2020-12-8 | toyota    | 1       | 0          |
| 2020-12-8 | toyota    | 1       | 2          |
| 2020-12-7 | toyota    | 0       | 2          |
| 2020-12-7 | toyota    | 0       | 1          |
| 2020-12-8 | honda     | 1       | 2          |
| 2020-12-8 | honda     | 2       | 1          |
| 2020-12-7 | honda     | 0       | 1          |
| 2020-12-7 | honda     | 1       | 2          |
| 2020-12-7 | honda     | 2       | 1          |

#### Output

| date_id   | make_name | unique_leads | unique_partners |
|-----------|-----------|--------------|-----------------|
| 2020-12-8 | toyota    | 2            | 3               |
| 2020-12-7 | toyota    | 1            | 2               |
| 2020-12-8 | honda     | 2            | 2               |
| 2020-12-7 | honda     | 3            | 2               |

#### Explanation

- For `2020-12-8` and `toyota`:
  - Unique leads: `[0, 1]` (2 leads).
  - Unique partners: `[0, 1, 2]` (3 partners).
- For `2020-12-7` and `toyota`:
  - Unique leads: `[0]` (1 lead).
  - Unique partners: `[1, 2]` (2 partners).
- Similarly, calculate for `honda` on each date.

---

### Thought Process

1. **Group Data by Date and Make**:
   - Group rows by `date_id` and `make_name`.

2. **Count Unique Identifiers**:
   - Use `COUNT(DISTINCT column_name)` to count unique values in `lead_id` and `partner_id`.

3. **Return Results**:
   - Select `date_id`, `make_name`, `unique_leads`, and `unique_partners`.

---

### Solution

```sql
SELECT 
    date_id,
    make_name,
    COUNT(DISTINCT lead_id) AS unique_leads,
    COUNT(DISTINCT partner_id) AS unique_partners
FROM 
    DailySales
GROUP BY 
    date_id, make_name;
```

---

### Solution Explanation

1. **Grouping by `date_id` and `make_name`**:
   - Ensures calculations are performed for each unique combination of date and product.

2. **Counting Distinct Values**:
   - `COUNT(DISTINCT lead_id)`: Counts the number of unique `lead_id`s for each group.
   - `COUNT(DISTINCT partner_id)`: Counts the number of unique `partner_id`s for each group.

3. **Result Order**:
   - The problem states the results can be returned in any order.

---

### Key Takeaways

- Use `GROUP BY` to segment data by specific columns.
- Use `COUNT(DISTINCT column)` to count unique values in grouped data.
- Ensure the output format matches the problem requirements.

---

### Related Topics

- SQL Aggregation
- Grouping Data
- Counting Unique Values
