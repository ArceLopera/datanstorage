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

SQL data types define the kind of data that can be stored in a table column. While exact data types can vary slightly between SQL dialects (e.g., MySQL, PostgreSQL, SQL Server, Oracle), here’s a comprehensive overview of the **main categories** of SQL data types used across most systems:

---

### **Numeric Data Types**

Used for storing numbers:

#### Integer Types:

| Type            | Description                              |
| --------------- | ---------------------------------------- |
| `TINYINT`       | Very small integers (e.g., -128 to 127)  |
| `SMALLINT`      | Small integers (e.g., -32,768 to 32,767) |
| `MEDIUMINT`     | Medium integers (MySQL specific)         |
| `INT`/`INTEGER` | Standard integer                         |
| `BIGINT`        | Very large integers                      |

#### Decimal/Fixed-point Types:

| Type                                                 | Description                                               |
| ---------------------------------------------------- | --------------------------------------------------------- |
| `DECIMAL(p,s)` / `NUMERIC(p,s)`                      | Exact numeric values with precision (`p`) and scale (`s`) |
| `p` = total digits, `s` = digits after decimal point |                                                           |

#### Floating-point Types:

| Type                          | Description                            |
| ----------------------------- | -------------------------------------- |
| `FLOAT`                       | Approximate floating-point number      |
| `REAL`                        | Higher precision floating-point number |
| `DOUBLE` / `DOUBLE PRECISION` | Even more precision                    |

---

### **String/Text Data Types**

Used for storing characters, text, or binary data.

#### Character Types:

| Type         | Description                                         |
| ------------ | --------------------------------------------------- |
| `CHAR(n)`    | Fixed-length string (padded with spaces if shorter) |
| `VARCHAR(n)` | Variable-length string, up to `n` characters        |

#### Text/Large Object Types:

| Type                                   | Description                              |
| -------------------------------------- | ---------------------------------------- |
| `TEXT`                                 | Large text block (e.g., article content) |
| `TINYTEXT` / `MEDIUMTEXT` / `LONGTEXT` | Different sizes in MySQL                 |
| `CLOB`                                 | Character Large Object (Oracle/DB2)      |

#### Binary Data Types:

| Type           | Description                                |
| -------------- | ------------------------------------------ |
| `BINARY(n)`    | Fixed-length binary data                   |
| `VARBINARY(n)` | Variable-length binary data                |
| `BLOB`         | Binary Large Object (for images/files etc) |

---

### **Date and Time Data Types**

Used to store date, time, or both.

| Type        | Description                               |
| ----------- | ----------------------------------------- |
| `DATE`      | Stores a date (YYYY-MM-DD)                |
| `TIME`      | Stores a time (HH\:MM\:SS)                |
| `DATETIME`  | Stores both date and time                 |
| `TIMESTAMP` | Stores date and time, often auto-updating |
| `YEAR`      | Stores a year (MySQL only)                |
| `INTERVAL`  | (PostgreSQL) Time intervals               |

---

### **Boolean Type**

| Type                                   | Description          |
| -------------------------------------- | -------------------- |
| `BOOLEAN` / `BOOL`                     | Stores TRUE or FALSE |
| (MySQL uses TINYINT(1) under the hood) |                      |

---

### **UUID / Unique Identifiers**

Used for globally unique values:

| Type               | Description                   |
| ------------------ | ----------------------------- |
| `UUID`             | Universally Unique Identifier |
| `UNIQUEIDENTIFIER` | Used in SQL Server            |

---

### **JSON and XML**

For structured data:

| Type   | Description                               |
| ------ | ----------------------------------------- |
| `JSON` | Stores JSON-formatted strings             |
| `XML`  | Stores XML documents (SQL Server, Oracle) |

---

### **Spatial / Geographic Types**

For storing geolocation data (supported in systems like PostgreSQL with PostGIS, MySQL):

| Type                                   | Description                     |
| -------------------------------------- | ------------------------------- |
| `GEOMETRY`                             | Base type for all geometry data |
| `POINT`, `LINESTRING`, `POLYGON`, etc. | Specific shapes                 |

---

### **Other Special Types**

* `ENUM('value1', 'value2', ...)`: Set of predefined values (MySQL)
* `SET`: A string object that can store zero or more values from a list (MySQL)
* `ARRAY`: A collection of values (PostgreSQL)
* `MONEY` / `SMALLMONEY`: Currency data (SQL Server)

---

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
