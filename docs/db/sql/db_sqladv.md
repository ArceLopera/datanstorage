## **Subqueries & CTEs (Common Table Expressions)**

### **Subqueries (`SELECT` inside `SELECT`)**
Subqueries allow you to use the result of one query within another query.

**Example: Find employees who earn more than the average salary**
```sql
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### **CTEs (`WITH` clause for readability)**
Common Table Expressions (CTEs) improve query readability by defining temporary result sets.

**Example: Using a CTE to list high-salary employees**
```sql
WITH HighEarners AS (
    SELECT first_name, last_name, salary
    FROM employees
    WHERE salary > 80000
)
SELECT * FROM HighEarners;
```

### **Recursive CTEs**
Recursive CTEs are used to handle hierarchical data such as organizational structures.

**Example: Retrieve an employee hierarchy**
```sql
WITH RECURSIVE EmployeeHierarchy AS (
    SELECT employee_id, manager_id, first_name, last_name, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.employee_id, e.manager_id, e.first_name, e.last_name, eh.level + 1
    FROM employees e
    INNER JOIN EmployeeHierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM EmployeeHierarchy;
```

---

## **Indexing & Performance Optimization**

### **What is an Index?**
Indexes improve query performance by allowing faster lookups.

### **Creating & Using Indexes (`CREATE INDEX`)**
Indexes speed up searches but can slow down insert/update operations.

**Example: Creating an index on the `last_name` column**
```sql
CREATE INDEX idx_lastname ON employees(last_name);
```

### **Understanding Execution Plans (`EXPLAIN`)**
The `EXPLAIN` statement helps analyze query performance.

**Example: Analyzing a query execution plan**
```sql
EXPLAIN ANALYZE SELECT * FROM employees WHERE last_name = 'Smith';
```

---

## **Transactions & ACID Principles**

### **`BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` – Managing Transactions**

Transactions ensure data integrity by grouping multiple operations.

**Example: Using transactions**
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT;
```

**Example: Rolling back a failed transaction**
```sql
BEGIN;
UPDATE orders SET status = 'Shipped' WHERE order_id = 5;
ROLLBACK;
```

### BEGIN vs. BEGIN TRANSACTION

+ BEGIN: In some databases, BEGIN alone is sufficient to start a transaction. However, in others (like SQL Server), BEGIN alone is ambiguous because it is also used in control flow structures (e.g., BEGIN...END for blocks of code).
+ BEGIN TRANSACTION (or BEGIN TRAN): This explicitly starts a transaction and is recommended for clarity, especially in databases like SQL Server.

|Database System|	Recommended Usage|
|---|---|
|PostgreSQL|	BEGIN; or START TRANSACTION;|
|MySQL|	START TRANSACTION; (preferred) or BEGIN; (can be used but conflicts with stored procedure syntax)|
|SQL Server|	BEGIN TRANSACTION; or BEGIN TRAN; (explicit, avoids ambiguity)|
|Oracle|	Transactions are implicitly managed, but BEGIN is used for PL/SQL blocks. Use SET TRANSACTION for control.|
|Microsoft SQL Server|	BEGIN TRANSACTION; or BEGIN TRAN; (explicit, avoids ambiguity)|

### Summary of Transaction Control Commands

|Command|	Purpose|	Example|
|---|---|---|
|BEGIN TRANSACTION|	Starts a new transaction.|	BEGIN TRANSACTION TransferFunds;|
|COMMIT|	Saves all changes made during the transaction.|	COMMIT;|
|ROLLBACK|	Undoes changes made during the transaction.|	ROLLBACK;|
|SAVEPOINT|	Creates a point in the transaction to which you can roll back.|	SAVEPOINT SP1;|
|ROLLBACK TO SAVEPOINT|	Rolls back the transaction to a specific savepoint.|	ROLLBACK TO SP1;|
|RELEASE SAVEPOINT|	Removes a savepoint from the transaction.|	RELEASE SAVEPOINT SP2;|


### **Scenario: Transferring Funds Between Accounts**
We want to transfer $500 from **Account A (ID: 1)** to **Account B (ID: 2)**. However, if Account B does not exist, we should roll back the transfer.

#### **SQL Transaction Example**
```sql
-- Start a new transaction
BEGIN TRANSACTION TransferFunds;

-- Deduct $500 from Account A
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;

-- Create a savepoint in case the next step fails
SAVEPOINT SP1;

-- Attempt to add $500 to Account B
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

-- Check if the update was successful
IF @@ROWCOUNT = 0 THEN
    -- If Account B does not exist, roll back to the savepoint
    ROLLBACK TO SP1;
    PRINT 'Transfer failed: Account B does not exist. Rolling back deduction.';
ELSE
    -- If everything is fine, commit the transaction
    COMMIT;
    PRINT 'Transfer successful!';
END IF;

-- Release the savepoint since it's no longer needed
RELEASE SAVEPOINT SP1;
```


**`IF @@ROWCOUNT = 0 THEN ROLLBACK TO SP1;`**  
   - If no rows were updated (meaning Account B doesn’t exist), rollback to the savepoint.

#### **What is `@@` in SQL?**  
In SQL, `@@` is used as a **prefix for system-defined global variables** (also called system functions in some databases). These variables store metadata about the current session, transaction, or system state.

##### **Usage of `@@` in Different Databases**
- **SQL Server**: `@@` is used for built-in system variables like `@@ROWCOUNT`, `@@TRANCOUNT`, `@@SERVERNAME`, etc.
- **MySQL & PostgreSQL**: They do not use `@@` for system variables. Instead:
  - MySQL uses `@@` for **session and global variables**, like `@@autocommit`.
  - PostgreSQL uses functions like `pg_backend_pid()` instead of `@@` variables.

---

###### **Common `@@` Variables in SQL Server**
| **Variable** | **Purpose** | **Example Usage** |
|-------------|------------|-------------------|
| `@@ROWCOUNT` | Returns the number of rows affected by the last statement. | `SELECT * FROM employees; PRINT @@ROWCOUNT;` |
| `@@TRANCOUNT` | Returns the number of active transactions in the current session. | `PRINT @@TRANCOUNT;` |
| `@@IDENTITY` | Returns the last inserted identity value. | `INSERT INTO users (name) VALUES ('John'); PRINT @@IDENTITY;` |
| `@@ERROR` | Returns the error code from the last statement. | `UPDATE employees SET salary = NULL; IF @@ERROR <> 0 PRINT 'Error occurred';` |
| `@@SERVERNAME` | Returns the name of the SQL Server instance. | `SELECT @@SERVERNAME;` |
| `@@VERSION` | Returns the version details of the SQL Server. | `SELECT @@VERSION;` |

---

##### **Examples of `@@` in Action**
###### **1. Using `@@ROWCOUNT` to Check Affected Rows**
```sql
UPDATE employees SET salary = salary * 1.1 WHERE department = 'Sales';
PRINT 'Rows affected: ' + CAST(@@ROWCOUNT AS VARCHAR);
```
If 5 employees in Sales received a 10% salary increase, `@@ROWCOUNT` would return `5`.

###### **2. Using `@@TRANCOUNT` to Check Active Transactions**
```sql
BEGIN TRANSACTION;
PRINT 'Active Transactions: ' + CAST(@@TRANCOUNT AS VARCHAR);
COMMIT;
```
This prints the current number of active transactions.

###### **3. Using `@@ERROR` for Error Handling**
```sql
BEGIN TRANSACTION;
UPDATE employees SET salary = NULL WHERE employee_id = 1;

IF @@ERROR <> 0
    PRINT 'Error occurred, rolling back!';
    ROLLBACK;
ELSE
    COMMIT;
```
If an error occurs, `ROLLBACK` prevents invalid changes.

---


### **ACID Properties (Atomicity, Consistency, Isolation, Durability)**
- **Atomicity**: Transactions are all-or-nothing.
- **Consistency**: Data remains valid before and after transactions.
- **Isolation**: Transactions execute independently.
- **Durability**: Committed data is permanently stored.

---

## **Advanced SQL Features**

### **`CASE` Statements – Conditional Logic**
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

### **Window Functions (`RANK()`, `DENSE_RANK()`, `ROW_NUMBER()`)**
Window functions perform calculations across table partitions.

**Example: Ranking employees by salary**
```sql
SELECT first_name, last_name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

### **Pivoting & Unpivoting Data**
Pivoting transforms row data into columns, while unpivoting does the reverse.

### **JSON Handling (`JSONB`, `->`, `->>`, `#>` in PostgreSQL)**
PostgreSQL supports querying JSON data stored in columns.

**Example: Extracting JSON fields**
```sql
SELECT data->'name' AS name FROM customers WHERE data->>'status' = 'active';
```

---

## **Stored Procedures & Triggers**

### **Creating & Executing Stored Procedures**
Stored procedures allow reusable SQL logic execution.

**Example: Creating a stored procedure**
```sql
CREATE PROCEDURE UpdateSalary(IN emp_id INT, IN new_salary DECIMAL(10,2))
LANGUAGE SQL
AS $$
    UPDATE employees SET salary = new_salary WHERE employee_id = emp_id;
$$;
```

### **Using Triggers for Automation**
Triggers execute actions automatically based on events.

**Example: Creating a trigger to update timestamps**
```sql
CREATE TRIGGER update_timestamp
BEFORE UPDATE ON employees
FOR EACH ROW
EXECUTE FUNCTION update_modified_column();
```

### **Cursors & Loops**
Cursors iterate over query results within stored procedures.

---

## **NoSQL Features in SQL Databases**

### **Working with JSON & XML**
Many relational databases support NoSQL-like JSON and XML processing.

### **Full-Text Search (`tsvector`, `MATCH AGAINST`)**
Full-text search allows efficient searching within text fields.

**Example: Using full-text search in PostgreSQL**
```sql
SELECT * FROM articles WHERE to_tsvector(content) @@ to_tsquery('database');
```

---

## **Security & Best Practices**

### **SQL Injection Prevention**
Use prepared statements to prevent SQL injection.

**Example: Using a parameterized query in PostgreSQL**
```sql
PREPARE stmt FROM 'SELECT * FROM users WHERE username = $1';
EXECUTE stmt('admin');
```

### **Role-Based Access Control (`GRANT`, `REVOKE`)**
Restrict access by assigning roles.

**Example: Granting read-only access**
```sql
GRANT SELECT ON employees TO readonly_user;
```

### **Data Encryption & Hashing (`MD5()`, `SHA256()`)**
Encryption and hashing protect sensitive data.

**Example: Storing hashed passwords**
```sql
UPDATE users SET password = SHA256('mysecurepassword');
```

---

These advanced SQL concepts help in optimizing, securing, and extending database functionality.



---
