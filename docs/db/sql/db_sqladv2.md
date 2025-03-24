
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