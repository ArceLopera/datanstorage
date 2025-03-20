
## ** Connecting SQL to Programming Languages**  

### ** What is ODBC? (Open Database Connectivity) 🛠️**  
ODBC (Open Database Connectivity) is a standard **interface** for connecting applications to databases, regardless of the database system.

💡 **Use Case:** ODBC allows applications written in **Python, PHP, JavaScript, C#, and more** to connect to MySQL, PostgreSQL, SQL Server, and other databases.  

- Install **ODBC Driver for MySQL** (Example for Linux):  
  ```sh
  sudo apt install unixODBC unixODBC-dev
  sudo apt install odbc-mariadb
  ```
- Configure ODBC in **odbc.ini** (Linux/Mac) or ODBC Data Source Administrator (Windows).

---
## ** SQL Libraries for Different Programming Languages**  


### ** JavaScript (Node.js) SQL Libraries 🟢**  
| Library | Database Support | Features |
|---------|-----------------|----------|
| **mysql2** | MySQL | Lightweight MySQL client |
| **pg (node-postgres)** | PostgreSQL | Native PostgreSQL driver |
| **sqlite3** | SQLite | SQLite support in Node.js |
| **knex.js** | MySQL, PostgreSQL, SQLite | SQL query builder for JavaScript |
| **Sequelize** | MySQL, PostgreSQL, SQLite | ORM for Node.js |

💡 **Example: Connecting Node.js to MySQL with `mysql2`**
```javascript
const mysql = require('mysql2');

const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'mypassword',
  database: 'mydb'
});

connection.query('SELECT * FROM users', (err, results) => {
  if (err) throw err;
  console.log(results);
});

connection.end();
```

---

### ** PHP SQL Libraries 🐘**  
| Library | Database Support | Features |
|---------|-----------------|----------|
| **MySQLi (Improved MySQL Extension)** | MySQL | Native MySQL support in PHP |
| **PDO (PHP Data Objects)** | MySQL, PostgreSQL, SQLite | Supports multiple databases |
| **pg_connect** | PostgreSQL | Native PostgreSQL driver |

💡 **Example: Connecting PHP to MySQL using PDO**
```php
<?php
$dsn = 'mysql:host=localhost;dbname=mydb;charset=utf8mb4';
$username = 'root';
$password = 'mypassword';

try {
    $pdo = new PDO($dsn, $username, $password);
    $stmt = $pdo->query("SELECT * FROM users");
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
        print_r($row);
    }
} catch (PDOException $e) {
    echo "Connection failed: " . $e->getMessage();
}
?>
```

---
### ** Python SQL Libraries 🐍**  
| Library | Database Support | Features |
|---------|-----------------|----------|
| **sqlite3** | SQLite | Built into Python, easy for local databases |
| **mysql-connector-python** | MySQL | Official MySQL connector for Python |
| **psycopg2** | PostgreSQL | Most popular PostgreSQL driver |
| **SQLAlchemy** | MySQL, PostgreSQL, SQLite | ORM (Object-Relational Mapping) for Python |
| **pyodbc** | All (via ODBC) | Universal ODBC connector |

💡 **Example: Connecting Python to MySQL with `mysql-connector-python`**
```python
import mysql.connector

conn = mysql.connector.connect(
    host="localhost",
    user="root",
    password="mypassword",
    database="mydb"
)
cursor = conn.cursor()
cursor.execute("SELECT * FROM users;")
for row in cursor.fetchall():
    print(row)
conn.close()
```

---

#### pyodbc
> pip install pyodbc

[pyodbc](https://github.com/mkleehammer/pyodbc/wiki) is a python-ODBC bridge library that also supports the DB-API 2.0 specification. and can connect to a vast number of databases, including MS SQL Server, MySQL, PostgreSQL, Oracle, Google Big Data, SQLite, among others.

> supporting the DB-API 2.0 means that code that uses pyodbc can look almost identical to code using SQLite

> Open Database Connectivity (ODBC) is the standard that allows using identical (or at least very similar) SQL statements for querying different Databases (DBMS). The designers of ODBC aimed to make it independent of database systems and operating systems. 

> ODBC accomplishes DBMS independence by using an ODBC driver as a translation layer between the application and the DBMS. The driver often has to be installed on the client operating system

##### Example connections

to connect to a DB, your often need to know the server/IP it is running on,
the name of the datanase, and username/password to access the databse

``` python
import pyodbc

server = "your_server"
db = "your_db"
user = "your_user"
password = "your_password"

```

##### MS SQL Server

``` python
connection_str = \
    'DRIVER={ODBC Driver 17 for SQL Server};' + \
    f'SERVER={server};'\
    f'DATABASE={db};'\
    f'UID={user};' \
    f'PWD={password}'
print(connection_str)

# # Connect to MS SQL Server
# conn = pyodbc.connect(connection_str)

```

##### MySQL

``` python
connection_str = \
    "DRIVER={MySQL ODBC 3.51 Driver};" \
    f"SERVER={server};" \
    f"DATABASE={db};"\
    f"UID={user};"\
    f"PASSWORD={password};"
print(connection_str)

# # Connect to MySQL
# conn = pyodbc.connect(connection_str) 

```

##### SQLite

> We don't to connect to SQLite via ODBC, because python can use these databases directly. <br>
> however, if we want to show this as a demo, we need to install the SQLite ODBC driver

> For Windows, you can get the SQLite ODBC driver [here](http://www.ch-werner.de/sqliteodbc/). Download "sqliteodbc.exe" if you are using 32-bit Python, or "sqliteodbc_w64.exe" if you are using 64-bit Python.

``` python
db="example.db"

connection_str = \
    "Driver=SQLite3 ODBC Driver;" \
    f"Database={db}"
    
print(connection_str)
conn = pyodbc.connect(connection_str)
c = conn.cursor()

for row in c.execute('SELECT * FROM stocks ORDER BY price'):
    print(row)

```
