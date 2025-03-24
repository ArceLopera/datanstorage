
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
