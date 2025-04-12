
## **`CASE` Statements – Conditional Logic**
The `CASE` statement allows conditional logic within SQL queries.

**Example: Categorizing salaries**
```sql
SELECT first_name, last_name,
    CASE
        WHEN salary > 80000 THEN 'High'
        WHEN salary BETWEEN 50000 AND 80000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_category
FROM employees;
```

## **Window Functions (`RANK()`, `DENSE_RANK()`, `ROW_NUMBER()`)**
Window functions perform calculations across table partitions.

**Example: Ranking employees by salary**
```sql
SELECT first_name, last_name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

## **Pivoting & Unpivoting Data**
Pivoting transforms row data into columns, while unpivoting does the reverse.

## **JSON Handling (`JSONB`, `->`, `->>`, `#>` in PostgreSQL)**
PostgreSQL supports querying JSON data stored in columns.

**Example: Extracting JSON fields**
```sql
SELECT data->'name' AS name FROM customers WHERE data->>'status' = 'active';
```

---
Sure! Here's a clear and practical tutorial for using `COALESCE` in SQL, suitable for beginners and intermediates:

---

## `COALESCE()`

The `COALESCE()` function in SQL returns the **first non-null value** from a list of expressions.

Useful when you're dealing with columns that may contain `NULL` values and you want to display something instead — like a default value or a fallback from another column.

---

```sql
COALESCE(expression1, expression2, ..., expressionN)
```

- It checks each expression **from left to right**.
- Returns the **first non-null** value it finds.
- If **all** expressions are `NULL`, it returns `NULL`.

---

Let's say you have a `users` table:

| id | first_name | nickname | username   |
|----|------------|----------|------------|
| 1  | John       | NULL     | johnny123  |
| 2  | NULL       | Sam      | sam2023    |
| 3  | NULL       | NULL     | guest001   |

You want to display the **best name available** in this order:
`nickname → first_name → username`

```sql
SELECT 
  id,
  COALESCE(nickname, first_name, username) AS display_name
FROM users;
```

**Result:**

| id | display_name |
|----|--------------|
| 1  | John         |
| 2  | Sam          |
| 3  | guest001     |

---

### Use Case: Default Values

Let’s say a column can be `NULL`, but you want to replace it with a default string:

```sql
SELECT 
  COALESCE(email, 'no-email@example.com') AS user_email
FROM users;
```

This ensures you never show a `NULL` — instead, you show a safe placeholder.

---

### `COALESCE()` vs `ISNULL()` or `IFNULL()`

- `COALESCE()` is **standard SQL** and portable across databases.
- `ISNULL()` is specific to **SQL Server**.
- `IFNULL()` is used in **MySQL** and **SQLite**.

```sql
-- SQL Server
ISNULL(column, 'default')

-- MySQL / SQLite
IFNULL(column, 'default')

-- Standard SQL (works everywhere)
COALESCE(column, 'default')
```

---

### Use in Aggregations

Imagine you’re summing order amounts, but some values are `NULL`:

```sql
SELECT 
  customer_id,
  SUM(COALESCE(order_total, 0)) AS total_spent
FROM orders
GROUP BY customer_id;
```

This avoids `NULL` values skewing your totals.

---

- `COALESCE()` returns the **first non-null** value.
- Great for fallback logic and cleaner results.
- Cross-database friendly.

---

**When to Use It**

- Displaying fallback values
- Cleaning up NULLs in output
- Creating user-friendly reports
- Providing default values for calculations

---
