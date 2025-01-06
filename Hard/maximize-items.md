### Maximize Items:

Table: Inventory

| Column Name    | Type    |
|----------------|---------|
| item_id        | int     |
| item_type      | varchar |
| item_category  | varchar |
| square_footage | decimal |

- `item_id` is the column of unique values for this table.
- Each row includes `item_id`, `item_type`, `item_category`, and `square_footage`.

Leetcode warehouse wants to maximize the number of items it can stock in a 500,000 square feet warehouse. It wants to stock as many **prime items** as possible, and afterward, use the remaining square footage to stock the most number of **non-prime items**.

Write a solution to find the number of **prime** and **non-prime** items that can be stored in the 500,000 square feet warehouse. Output the `item_type` with `prime_eligible` followed by `not_prime` and the maximum number of items that can be stocked.

### Notes:
- Item count must be a whole number (integer).
- If the count for the `not_prime` category is 0, you should output `0` for that particular category.
- Return the result table ordered by `item_count` in descending order.

---

### Example:

#### Input:

Inventory table:

| item_id | item_type      | item_category | square_footage |
|---------|----------------|---------------|----------------|
| 1374    | prime_eligible | Watches       | 68.00          |
| 4245    | not_prime      | Art           | 26.40          |
| 5743    | prime_eligible | Software      | 325.00         |
| 8543    | not_prime      | Clothing      | 64.50          |
| 2556    | not_prime      | Shoes         | 15.00          |
| 2452    | prime_eligible | Scientific    | 85.00          |
| 3255    | not_prime      | Furniture     | 22.60          |
| 1672    | prime_eligible | Beauty        | 8.50           |
| 4256    | prime_eligible | Furniture     | 55.50          |
| 6325    | prime_eligible | Food          | 13.20          |

#### Output:

| item_type      | item_count |
|----------------|------------|
| prime_eligible | 5400       |
| not_prime      | 8          |

#### Explanation:
1. **Prime-eligible items**:
   - Total square footage of prime items = 555.20 (68 + 325 + 85 + 8.50 + 55.50 + 13.20).
   - Number of combinations = \( \text{floor}(500,000 / 555.20) = 900 \).
   - Total items = \( 900 \times 6 = 5400 \).
   - These items occupy \( 499,680 \) square feet.

2. **Non-prime items**:
   - Remaining space = \( 500,000 - 499,680 = 320 \).
   - Total square footage of non-prime items = 128.50 (26.40 + 64.50 + 15.00 + 22.60).
   - Number of combinations = \( \text{floor}(320 / 128.50) = 2 \).
   - Total items = \( 2 \times 4 = 8 \).

---

### Thought Process:
1. **Aggregate Prime Items**:
   - Calculate the total square footage and count of `prime_eligible` items.
   - Compute the maximum number of combinations that can fit in 500,000 square feet.
   - Calculate the total prime items that can be stocked.

2. **Calculate Remaining Space**:
   - Deduct the square footage used by prime items from the total warehouse space.

3. **Aggregate Non-Prime Items**:
   - Calculate the total square footage and count of `not_prime` items.
   - Compute the maximum number of combinations that fit in the remaining space.
   - Calculate the total non-prime items that can be stocked.

4. **Output Results**:
   - Display the `item_type` and `item_count` for both prime and non-prime items.
   - If no non-prime items can be stocked, display `0`.
   - Order results by `item_count` in descending order.

---

### Query:

```sql
WITH prime_footage_data AS (
    SELECT 
        item_type, 
        COUNT(item_category) AS item_count, 
        SUM(square_footage) AS total_prime_footage
    FROM inventory
    WHERE item_type = 'prime_eligible'
    GROUP BY item_type
),

prime_item_calculations AS (
    SELECT 
        item_count * FLOOR(500000 / total_prime_footage) AS total_prime_items, 
        FLOOR(500000 / total_prime_footage) AS prime_combinations
    FROM prime_footage_data
),

remaining_space AS (
    SELECT 
        500000 - (total_prime_footage * prime_combinations) AS remaining_space_for_non_prime
    FROM prime_footage_data, prime_item_calculations
),

non_prime_footage_data AS (
    SELECT 
        item_type, 
        COUNT(item_category) AS item_count, 
        SUM(square_footage) AS total_non_prime_footage
    FROM inventory
    WHERE item_type = 'not_prime'
    GROUP BY item_type
),

non_prime_item_calculations AS (
    SELECT 
        item_count * FLOOR(remaining_space_for_non_prime / total_non_prime_footage) AS total_non_prime_items
    FROM non_prime_footage_data, remaining_space
)

SELECT 
    'prime_eligible' AS item_type, 
    total_prime_items AS item_count
FROM prime_item_calculations
UNION ALL
SELECT 
    'not_prime' AS item_type, 
    IFNULL(total_non_prime_items, 0) AS item_count
FROM non_prime_item_calculations;
