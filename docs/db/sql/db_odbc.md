
## pyodbc
> pip install pyodbc

[pyodbc](https://github.com/mkleehammer/pyodbc/wiki) is a python-ODBC bridge library that also supports the DB-API 2.0 specification. and can connect to a vast number of databases, including MS SQL Server, MySQL, PostgreSQL, Oracle, Google Big Data, SQLite, among others.

> supporting the DB-API 2.0 means that code that uses pyodbc can look almost identical to code using SQLite

> Open Database Connectivity (ODBC) is the standard that allows using identical (or at least very similar) SQL statements for querying different Databases (DBMS). The designers of ODBC aimed to make it independent of database systems and operating systems. 

> ODBC accomplishes DBMS independence by using an ODBC driver as a translation layer between the application and the DBMS. The driver often has to be installed on the client operating system

### Example connections

to connect to a DB, your often need to know the server/IP it is running on,
the name of the datanase, and username/password to access the databse

``` python
import pyodbc

server = "your_server"
db = "your_db"
user = "your_user"
password = "your_password"

```

### MS SQL Server

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

### MySQL

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

### SQLite

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
