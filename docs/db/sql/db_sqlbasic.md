## **Basic SQL Commands**  

### `SELECT` – Retrieving Data  
The `SELECT` statement is used to retrieve data from a database. You can specify which columns to retrieve or use `*` to get all columns.

**Example 1: Select all columns from a table**
```sql
SELECT * FROM employees;
```

**Example 2: Select specific columns**
```sql
SELECT first_name, last_name, salary FROM employees;
```

**Example 3: Using `WHERE` to filter results**
```sql
SELECT first_name, last_name FROM employees WHERE department = 'HR';
```

### `INSERT` – Adding Records  
The `INSERT` statement is used to add new records into a table.

**Example 1: Inserting a full row**
```sql
INSERT INTO employees (first_name, last_name, department, salary)
VALUES ('John', 'Doe', 'IT', 60000);
```

**Example 2: Inserting multiple rows**
```sql
INSERT INTO employees (first_name, last_name, department, salary)
VALUES ('Jane', 'Smith', 'Finance', 70000),
       ('Alice', 'Johnson', 'HR', 65000);
```

### `UPDATE` – Modifying Data  
The `UPDATE` statement is used to modify existing records.

**Example: Updating a salary**
```sql
UPDATE employees SET salary = 75000 WHERE first_name = 'John' AND last_name = 'Doe';
```

**Example: Updating multiple fields**
```sql
UPDATE employees SET department = 'Marketing', salary = 72000 WHERE employee_id = 5;
```

### `DELETE` – Removing Records  
The `DELETE` statement is used to remove records from a table.

**Example: Deleting a specific record**
```sql
DELETE FROM employees WHERE employee_id = 10;
```

**Example: Deleting all employees from a department**
```sql
DELETE FROM employees WHERE department = 'HR';
```

## **Working with Tables**  

### `CREATE TABLE` – Defining Structure  
The `CREATE TABLE` statement is used to define a new table.

**Example: Creating an `employees` table**
```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE
);
```

### `ALTER TABLE` – Modifying Schema  
The `ALTER TABLE` statement allows modifications to an existing table.

**Example: Adding a new column**
```sql
ALTER TABLE employees ADD email VARCHAR(100);
```

**Example: Modifying a column data type**
```sql
ALTER TABLE employees MODIFY salary DECIMAL(12,2);
```

**Example: Renaming a column**
```sql
ALTER TABLE employees RENAME COLUMN department TO dept_name;
```

### `DROP TABLE` – Deleting a Table  
The `DROP TABLE` statement removes a table and all its data.

**Example:**
```sql
DROP TABLE employees;
```

### Data Types (`INT`, `VARCHAR`, `DATE`, etc.)  
Common SQL data types include:
- `INT` – Integer values (e.g., `employee_id INT`)
- `VARCHAR(n)` – Variable-length string (`name VARCHAR(100)`)
- `DATE` – Stores date values (e.g., `hire_date DATE`)
- `DECIMAL(m,d)` – Decimal numbers with `m` digits and `d` decimal places (e.g., `salary DECIMAL(10,2)`)

## **Filtering Data**  

### `WHERE` – Conditional Filtering  
The `WHERE` clause is used to filter results based on conditions.

**Example: Filtering by salary**
```sql
SELECT * FROM employees WHERE salary > 50000;
```

**Example: Filtering by multiple conditions**
```sql
SELECT * FROM employees WHERE department = 'IT' AND salary > 60000;
```

### `ORDER BY` – Sorting Results  
The `ORDER BY` clause sorts results in ascending (`ASC`) or descending (`DESC`) order.

**Example: Sorting by salary (descending)**
```sql
SELECT * FROM employees ORDER BY salary DESC;
```

**Example: Sorting by department and then by salary**
```sql
SELECT * FROM employees ORDER BY department ASC, salary DESC;
```

### `LIMIT` – Restricting Output  
The `LIMIT` clause restricts the number of rows returned.

**Example: Getting the top 5 highest salaries**
```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;
```

**Example: Paginating results (offset 10, limit 5)**
```sql
SELECT * FROM employees ORDER BY employee_id ASC LIMIT 5 OFFSET 10;
```

### `DISTINCT` – Removing Duplicates  
The `DISTINCT` keyword removes duplicate values in query results.

**Example: Getting unique department names**
```sql
SELECT DISTINCT department FROM employees;
```

**Example: Getting unique job titles from a `jobs` table**
```sql
SELECT DISTINCT job_title FROM jobs;
```

These basic SQL commands and filtering techniques help in effectively managing and retrieving data from a relational database system.

---
