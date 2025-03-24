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
