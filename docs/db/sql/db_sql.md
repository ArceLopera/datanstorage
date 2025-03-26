
|  **Topic**                 | **Key Concepts** |
|---------------------------|----------------|
| [**Introduction to SQL**](#introduction-to-sql) | [What is SQL](#what-is-sql), [SQL vs NoSQL](#comparing-sql-vs-nosql), [Installation (SQLite, MySQL, PostgreSQL)](db_sqlinstall.md#13-installing--setting-up-sql) |
|  [**Basic SQL Commands**](db_sqlbasic.md) | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
|  [**Working with Tables**](db_sqlbasic.md#working-with-tables) | `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`, Data Types (`INT`, `VARCHAR`, etc.) |
|  [**Filtering Data**](db_sqlbasic.md#filtering-data) | `WHERE`, `ORDER BY`, `LIMIT`, `DISTINCT` |
|  [**SQL Joins & Relationships**](db_sqljoin.md) | `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`, `SELF JOIN`, `CROSS JOIN` |
| [**Grouping & Aggregation**](db_sqljoin.md#grouping-aggregation) | `GROUP BY`, `HAVING`, Aggregate Functions (`COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`) |
|  [**Subqueries & CTEs**](db_sqlsubquery.md) | Subqueries (`SELECT` inside `SELECT`), CTEs (`WITH` clause), Recursive CTEs |
|  [**Indexing & Performance Optimization**](db_sqlindex.md) | `CREATE INDEX`, Execution Plans (`EXPLAIN`), Query Optimization |
| [**Transactions & ACID Principles**](db_sqlacid.md) | `BEGIN`, `COMMIT`, `ROLLBACK`, ACID Properties (Atomicity, Consistency, Isolation, Durability) |
| [**Advanced SQL Features**](db_sqladv2.md) | `CASE` Statements, Window Functions (`RANK()`, `DENSE_RANK()`, `ROW_NUMBER()`), Pivoting & JSON Handling |
|  [**Stored Procedures & Triggers**](db_sqlproc.md) | Creating & Executing Stored Procedures, Triggers, Cursors & Loops |
|  [**NoSQL Features in SQL**](db_sqlproc.md#nosql-features-in-sql-databases) | Working with JSON & XML, Full-Text Search (`tsvector`, `MATCH AGAINST`) |
| [**Security & Best Practices**](db_sqlproc.md#security-best-practices) | SQL Injection Prevention, Role-Based Access (`GRANT`, `REVOKE`), Data Encryption (`MD5()`, `SHA256()`) |


## **Introduction to SQL**  
---

### **What is SQL?**  
SQL (**Structured Query Language**) is used to manage **relational databases**, allowing you to:  
✔ Store, retrieve, and manipulate data  
✔ Define database structures (tables, indexes, etc.)  
✔ Control access and security  

SQL is used in **Relational Database Management Systems (RDBMS)** such as:  
- **MySQL** 🐬  
- **PostgreSQL** 🐘  
- **SQLite** 🛢️  
- **Microsoft SQL Server** 🏢  
- **Oracle Database** 🔥  

---

## **Comparing SQL vs. NoSQL**  
| Feature | SQL (Relational DBs) | NoSQL (Non-Relational DBs) |
|---------|----------------------|----------------------------|
| **Structure** | Tables (rows & columns) | Documents, Key-Value, Graphs |
| **Schema** | Fixed, predefined | Flexible, dynamic |
| **Scalability** | Vertical (scale-up) | Horizontal (scale-out) |
| **Use Case** | Structured data (banking, e-commerce) | Unstructured data (social media, real-time apps) |

Popular **NoSQL** databases: **MongoDB** 🍃, **Redis** 🔥, **Cassandra** 💾  

---
