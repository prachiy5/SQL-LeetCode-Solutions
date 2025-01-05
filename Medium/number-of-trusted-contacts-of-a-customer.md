### Number of Trusted Contacts of a Customer
We are given three tables:

#### Table: `Customers`

| Column Name   | Type    |
|---------------|---------|
| customer_id   | int     |
| customer_name | varchar |
| email         | varchar |

- `customer_id` is the primary key.
- Each row contains the name and email of a customer of an online shop.

#### Table: `Contacts`

| Column Name   | Type    |
|---------------|---------|
| user_id       | int     |
| contact_name  | varchar |
| contact_email | varchar |

- `(user_id, contact_email)` is the primary key.
- Each row contains the name and email of a contact for a customer (`user_id`).
- A contact may or may not exist in the `Customers` table.

#### Table: `Invoices`

| Column Name  | Type |
|--------------|------|
| invoice_id   | int  |
| price        | int  |
| user_id      | int  |

- `invoice_id` is the primary key.
- Each row indicates that `user_id` has an invoice with `invoice_id` and a specified `price`.

### Task
Write a SQL query to compute the following for each `invoice_id`:
1. `customer_name`: The name of the customer associated with the invoice.
2. `price`: The price of the invoice.
3. `contacts_cnt`: The number of contacts related to the customer.
4. `trusted_contacts_cnt`: The number of contacts related to the customer who are also customers (i.e., their email exists in the `Customers` table).

Return the result table ordered by `invoice_id`.

### Example
#### Input:
**Customers table:**

| customer_id | customer_name | email              |
|-------------|---------------|--------------------|
| 1           | Alice         | alice@leetcode.com |
| 2           | Bob           | bob@leetcode.com   |
| 13          | John          | john@leetcode.com  |
| 6           | Alex          | alex@leetcode.com  |

**Contacts table:**

| user_id | contact_name | contact_email      |
|---------|--------------|--------------------|
| 1       | Bob          | bob@leetcode.com   |
| 1       | John         | john@leetcode.com  |
| 1       | Jal          | jal@leetcode.com   |
| 2       | Omar         | omar@leetcode.com  |
| 2       | Meir         | meir@leetcode.com  |
| 6       | Alice        | alice@leetcode.com |

**Invoices table:**

| invoice_id | price | user_id |
|------------|-------|---------|
| 77         | 100   | 1       |
| 88         | 200   | 1       |
| 99         | 300   | 2       |
| 66         | 400   | 2       |
| 55         | 500   | 13      |
| 44         | 60    | 6       |

#### Output:

| invoice_id | customer_name | price | contacts_cnt | trusted_contacts_cnt |
|------------|---------------|-------|--------------|-----------------------|
| 44         | Alex          | 60    | 1            | 1                     |
| 55         | John          | 500   | 0            | 0                     |
| 66         | Bob           | 400   | 2            | 0                     |
| 77         | Alice         | 100   | 3            | 2                     |
| 88         | Alice         | 200   | 3            | 2                     |
| 99         | Bob           | 300   | 2            | 0                     |

#### Explanation:
- **Alice:** Has three contacts (Bob, John, Jal). Bob and John are trusted contacts as their emails exist in the `Customers` table.
- **Bob:** Has two contacts (Omar, Meir). None are trusted contacts.
- **Alex:** Has one contact (Alice), and Alice is a trusted contact.
- **John:** Has no contacts.

---

### Thought Process
1. **Join Tables:**
   - Join `Invoices` with `Customers` to associate invoices with customer names.
   - Left join `Contacts` to include the list of contacts for each user.
   - Left join `Customers` again to determine which contacts are also trusted (exist in the `Customers` table).

2. **Count Contacts:**
   - Use `COUNT(ct.contact_email)` to count all contacts for a user.
   - Use `COUNT(DISTINCT cc.email)` to count trusted contacts (contacts whose email exists in the `Customers` table).

3. **Group and Aggregate:**
   - Group by `invoice_id` to calculate the counts for each invoice.

4. **Order Results:**
   - Order the result by `invoice_id` as required.

---

### SQL Query
```sql
SELECT 
    i.invoice_id,
    c.customer_name,
    i.price,
    COUNT(ct.contact_email) AS contacts_cnt,  -- Count all contacts
    COUNT(DISTINCT cc.email) AS trusted_contacts_cnt  -- Count trusted contacts
FROM Invoices i
JOIN Customers c
    ON i.user_id = c.customer_id
LEFT JOIN Contacts ct
    ON i.user_id = ct.user_id  
LEFT JOIN Customers cc
    ON ct.contact_email = cc.email  
GROUP BY i.invoice_id
ORDER BY i.invoice_id;
