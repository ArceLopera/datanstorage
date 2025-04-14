## **Subqueries & CTEs (Common Table Expressions)**

### **Subqueries (`SELECT` inside `SELECT`)**
Subqueries allow you to use the result of one query within another query.

**Example: Find employees who earn more than the average salary**
```sql
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

### What is a CTE (Common Table Expression)?

A **CTE** is a temporary, named result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement.  
It exists only during the execution of the query — think of it like a temporary view that you can use just within that query.

### Why Use CTEs?
- Improve **readability** and **organization** for complex queries.
- Avoid repeating the same subquery multiple times.
- Enable **recursive queries**.
- Break down complicated queries into logical, easy-to-read sections.

---

### Types of CTE Usage

#### **Simple CTE**
Used to structure a subquery in a readable way.

```sql
WITH sales_total AS (
    SELECT employee_id, SUM(sales) AS total_sales
    FROM sales
    GROUP BY employee_id
)
SELECT *
FROM sales_total
WHERE total_sales > 5000;
```

**CTEs (`WITH` clause for readability)**
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

---

#### **Chained / Multiple CTEs**
You can define multiple CTEs at once.

```sql
WITH cte1 AS (
    SELECT * FROM products WHERE price > 100
),
cte2 AS (
    SELECT * FROM cte1 WHERE category = 'Electronics'
)
SELECT * FROM cte2;
```

---

#### **Recursive CTE**
When a CTE refers to itself — useful for hierarchical or tree-like data (like org charts or category trees).

##### Example: Counting down from 5

```sql
WITH RECURSIVE countdown AS (
    SELECT 5 AS number
    UNION ALL
    SELECT number - 1
    FROM countdown
    WHERE number > 1
)
SELECT * FROM countdown;
```

- `UNION ALL` is required in recursive CTEs.
- The first `SELECT` is the **anchor member**.
- The second `SELECT` is the **recursive member**.
- `WHERE` clause limits recursion.

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

### **CTE with DML (INSERT/UPDATE/DELETE)**
You can use CTEs to simplify data modifications.

##### Example: Updating top salespeople

```sql
WITH top_sales AS (
    SELECT employee_id
    FROM sales
    GROUP BY employee_id
    HAVING SUM(sales) > 5000
)
UPDATE employees
SET bonus = 1000
WHERE employee_id IN (SELECT employee_id FROM top_sales);
```

---

### CTE vs Subquery

| CTE                            | Subquery                        |
|:--------------------------------|:--------------------------------|
| Improves readability for complex queries | Can get nested and hard to read |
| Can be recursive               | Cannot be recursive             |
| Can be referenced multiple times in the same query | Must be repeated for multiple uses |
| Exists only during the query execution | Exists only within its surrounding query |

---

- CTEs **don’t persist** — they’re temporary.
- Available in most modern RDBMS systems: PostgreSQL, SQL Server, Oracle, MySQL 8+, etc.
- You can **nest CTEs** and even use them in `JOIN`s.

---

**Summary**

CTEs are:

- **Temporary, named query results**
- Great for **breaking down complex queries**
- Essential for **recursive operations**
- Often improve **query clarity and maintainability**

---
