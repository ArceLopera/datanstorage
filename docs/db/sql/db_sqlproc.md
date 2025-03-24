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

---
