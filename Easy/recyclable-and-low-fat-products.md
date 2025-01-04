# Recyclable and Low Fat Products
### Problem

#### Table: Products

| Column Name | Type    |
|-------------|---------|
| product_id  | int     |
| low_fats    | enum    |
| recyclable  | enum    |

- `product_id` is the primary key.
- `low_fats` is an ENUM with values ('Y', 'N'). 'Y' indicates the product is low fat, and 'N' indicates it is not.
- `recyclable` is an ENUM with values ('Y', 'N'). 'Y' indicates the product is recyclable, and 'N' indicates it is not.

#### Task
Find the `product_id` of products that are both low fat (`low_fats = 'Y'`) and recyclable (`recyclable = 'Y'`).

### Example

#### Input

**Products Table**:

| product_id | low_fats | recyclable |
|------------|----------|------------|
| 0          | Y        | N          |
| 1          | Y        | Y          |
| 2          | N        | Y          |
| 3          | Y        | Y          |
| 4          | N        | N          |

#### Output

| product_id |
|------------|
| 1          |
| 3          |

#### Explanation
- Products `1` and `3` satisfy both conditions: they are low fat and recyclable.
- Product `0` is low fat but not recyclable.
- Product `2` is recyclable but not low fat.
- Product `4` is neither low fat nor recyclable.

### Thought Process

1. **Understand the Problem**:
   - Identify products where both conditions are true (`low_fats = 'Y'` and `recyclable = 'Y'`).

2. **Apply Filtering**:
   - Use the `WHERE` clause to filter rows that satisfy both conditions.

3. **Return Relevant Data**:
   - Select only the `product_id` column since that is required in the output.

4. **Order**:
   - No specific order is required, so the result can be returned in any order.

### Solution

```sql
SELECT product_id
FROM products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```

### Solution Explanation

1. **SELECT Clause**:
   - Specifies that we only need the `product_id` column in the output.

2. **WHERE Clause**:
   - Filters rows where `low_fats = 'Y'` (the product is low fat) and `recyclable = 'Y'` (the product is recyclable).

3. **Output**:
   - The result will contain the IDs of products satisfying both conditions.

### Key Takeaways

- **Logical Operators (`AND`)**: Combine multiple conditions in the `WHERE` clause.
- **Filtering Data**: Use `WHERE` to filter rows based on specific criteria.
- **Output Specific Columns**: Select only the necessary columns to keep the query efficient.

### Related Topics

- SQL Filtering with `WHERE`
- Logical Operators in SQL
- SELECT Clause
