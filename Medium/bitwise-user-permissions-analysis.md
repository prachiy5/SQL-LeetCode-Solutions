### Bitwise User Permissions Analysis
You are tasked with analyzing a table called `user_permissions`, which contains information about user IDs and their respective permission levels encoded as integers. Each bit in the `permissions` integer represents a different access level or feature that a user has. The goal is to calculate the following metrics:

1. **common_perms**: The access levels that are granted to all users. This is computed using a bitwise AND operation across all rows in the `permissions` column.
2. **any_perms**: The access levels that are granted to at least one user. This is computed using a bitwise OR operation across all rows in the `permissions` column.

The result table should return these metrics in the following format:

| common_perms | any_perms |
|--------------|-----------|
| ...          | ...       |

### Example Input

#### user_permissions Table

| user_id | permissions |
|---------|-------------|
| 1       | 5           |
| 2       | 12          |
| 3       | 7           |
| 4       | 3           |

### Example Output

| common_perms | any_perms |
|--------------|-----------|
| 0            | 15        |

### Explanation

#### Bitwise AND for `common_perms`:
1. Convert permissions to binary:
   - User 1: 5 (binary `0101`)
   - User 2: 12 (binary `1100`)
   - User 3: 7 (binary `0111`)
   - User 4: 3 (binary `0011`)
2. Perform bitwise AND:
   - `0101 & 1100 & 0111 & 0011 = 0000`
3. Result: `0`

#### Bitwise OR for `any_perms`:
1. Convert permissions to binary:
   - User 1: 5 (binary `0101`)
   - User 2: 12 (binary `1100`)
   - User 3: 7 (binary `0111`)
   - User 4: 3 (binary `0011`)
2. Perform bitwise OR:
   - `0101 | 1100 | 0111 | 0011 = 1111`
3. Result: `15`

### Thought Process

1. Use SQL aggregate functions to compute bitwise operations:
   - **BIT_AND**: Computes the bitwise AND across all rows of a column.
   - **BIT_OR**: Computes the bitwise OR across all rows of a column.
2. Apply these functions directly to the `permissions` column in the `user_permissions` table.
3. Return the result in a single row containing both `common_perms` and `any_perms`.

### Query
```sql
SELECT 
    BIT_AND(permissions) AS common_perms,
    BIT_OR(permissions) AS any_perms
FROM 
    user_permissions;
