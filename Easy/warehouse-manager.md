# Warehouse Manager

### Problem

#### Tables

**Warehouse**:

| Column Name  | Type    |
|--------------|---------|
| name         | varchar |
| product_id   | int     |
| units        | int     |

- `(name, product_id)` is the primary key.
- Contains information about products stored in different warehouses and the number of units of each product.

**Products**:

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| product_name  | varchar |
| Width         | int     |
| Length        | int     |
| Height        | int     |

- `product_id` is the primary key.
- Contains information about product dimensions (Width, Length, and Height in feet).

#### Task

Calculate the total cubic feet of volume occupied by inventory in each warehouse. Return the results in any order.

### Example

#### Input

**Warehouse** table:

| name       | product_id | units |
|------------|------------|-------|
| LCHouse1   | 1          | 1     |
| LCHouse1   | 2          | 10    |
| LCHouse1   | 3          | 5     |
| LCHouse2   | 1          | 2     |
| LCHouse2   | 2          | 2     |
| LCHouse3   | 4          | 1     |

**Products** table:

| product_id | product_name | Width | Length | Height |
|------------|--------------|-------|--------|--------|
| 1          | LC-TV        | 5     | 50     | 40     |
| 2          | LC-KeyChain  | 5     | 5      | 5      |
| 3          | LC-Phone     | 2     | 10     | 10     |
| 4          | LC-T-Shirt   | 4     | 10     | 20     |

#### Output

| warehouse_name | volume |
|----------------|--------|
| LCHouse1       | 12250  |
| LCHouse2       | 20250  |
| LCHouse3       | 800    |

#### Explanation

1. **Volume Calculation for Each Product:**
   - `LC-TV` (product_id = 1): 5 × 50 × 40 = 10,000 cubic feet.
   - `LC-KeyChain` (product_id = 2): 5 × 5 × 5 = 125 cubic feet.
   - `LC-Phone` (product_id = 3): 2 × 10 × 10 = 200 cubic feet.
   - `LC-T-Shirt` (product_id = 4): 4 × 10 × 20 = 800 cubic feet.

2. **Total Volume per Warehouse:**
   - **LCHouse1:**
     - 1 unit of `LC-TV` + 10 units of `LC-KeyChain` + 5 units of `LC-Phone`.
     - Total: 1 × 10,000 + 10 × 125 + 5 × 200 = 12,250 cubic feet.
   - **LCHouse2:**
     - 2 units of `LC-TV` + 2 units of `LC-KeyChain`.
     - Total: 2 × 10,000 + 2 × 125 = 20,250 cubic feet.
   - **LCHouse3:**
     - 1 unit of `LC-T-Shirt`.
     - Total: 1 × 800 = 800 cubic feet.

---

### Solution

```sql
SELECT 
    name AS warehouse_name, 
    SUM(units * Width * Height * Length) AS volume
FROM Warehouse w
LEFT JOIN Products p
ON w.product_id = p.product_id
GROUP BY name;
```

---

### Solution Explanation

1. **Join Tables:**
   - Use a `LEFT JOIN` to combine `Warehouse` and `Products` tables based on the `product_id` column.
   - This ensures we have the product dimensions for each item in the warehouse.

2. **Calculate Volume:**
   - For each row, calculate the volume occupied by the product using:
     - `units × Width × Height × Length`.
   - Use `SUM` to aggregate the total volume for each warehouse.

3. **Group by Warehouse:**
   - Use `GROUP BY name` to calculate the total volume for each warehouse.

4. **Select Columns:**
   - Select `name` as `warehouse_name` and the calculated volume as `volume`.

---

### Key Takeaways

- **Join Tables for Complete Data:** Combine related tables to access all required fields.
- **Aggregate Functions:** Use `SUM` to calculate total values across grouped rows.
- **GROUP BY Clause:** Group rows by a column to aggregate data at that level.

---

### Related Topics

- SQL Joins
- Aggregation and Grouping
- Arithmetic Operations in SQL
