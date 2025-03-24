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
