
SQLite is an open-source library that provides a lightweight **disk-based** database.

> 1. sqlite it  doesn’t require a separate server process
> 2. allows accessing the database using a nonstandard variant of the SQL query language. 
> 3. provides a SQL interface compliant with the python's DB-API 2.0 specification described by [PEP 249][1].

applications can use SQLite for internal data storage. It’s also possible to prototype an application using SQLite and then port the code to a larger database such as PostgreSQL or Oracle.

[1]: https://www.python.org/dev/peps/pep-0249

To use the module, you must first create a `Connection` object that represents the database.

A connection object's key methods are:

1. `cursor()`
    returns a cursor object that can execute queries and retrieve results

2. `commit()`
    submits the current transaction to the DB. If you don’t call this method, anything you did since the last call to commit() is not visible from other database connections. If you wonder why you don’t see the data you’ve written to the database, please check you didn’t forget to call this method.

3. `rollback()`
    rolls back any changes to the database since the last call to commit().

4. `close()`
    closes the database connection. Note that this does not automatically call commit(). If you just close your database connection without calling commit() first, your changes will be lost!

in our example the data will be stored in a local file called `example.db`:

``` python
import sqlite3
conn = sqlite3.connect('example.db')
```

> You can also supply the special name `:memory:` to create a database in RAM.

Once you have a Connection, you can create a `Cursor` object and call its `execute()` method to perform SQL commands:

``` python	
c = conn.cursor()

# Create table called stocks
c.execute('''
    CREATE TABLE stocks (
        date text, 
        trans text, 
        symbol text, 
        qty real, 
        price real
        )
    ''')

# Save (commit) the changes
conn.commit()

# We can also close the connection if we are done with it.
# Just be sure any changes have been committed or they will be lost.
conn.close()
```

since we don't want to forget closing the connection, here's a nice utility function
that opens a connection and returns a handy cursor object

``` python
from contextlib import contextmanager

@contextmanager
def sqlite3_connect(database, *args, **kwargs):
    conn = sqlite3.connect(database, *args, **kwargs)
    try:
        cursor = conn.cursor()
        yield (conn, cursor)
    finally:
        conn.close()

```
    

and here's how `sqlite3_connect()` can be used: 

``` python

with sqlite3_connect('example.db') as [conn, c]:

    # Insert a row of data
    c.execute("INSERT INTO stocks VALUES ('2008-01-05','BUY','AAPL',120,37.14)")

    # Save (commit) the changes
    conn.commit()
    
# closing is done for us

```

Usually your SQL operations will need to use values from Python variables like people's names, and fields from forms.

DO NOT assemble your SQL using Python’s string operations because doing so is insecure; it makes your program vulnerable to an SQL injection attack 

![XKCD](https://imgs.xkcd.com/comics/exploits_of_a_mom.png)

Instead, use the API’s parameter substitution - Put ? as a placeholder wherever you want to use a value, and then provide a tuple of values as the second argument to the cursor’s execute() method.

``` python
with sqlite3_connect('example.db') as [conn, c]:

    # DO NOT use str.format or f-strings or any other way to embed your variables into strings

    # INSTEAD, use the sanitized substitution capability of the execute() function
    t = ('RHAT',)
    c.execute('SELECT * FROM stocks WHERE symbol=?', t)
    print(c.fetchone())

    # Larger example that inserts many records at a time
    purchases = [('2006-03-28', 'BUY', 'IBM', 1000, 45.00),
                 ('2006-04-05', 'BUY', 'MSFT', 1000, 72.00),
                 ('2006-04-06', 'SELL', 'IBM', 500, 53.00),
                ]
    c.executemany('INSERT INTO stocks VALUES (?,?,?,?,?)', purchases)
    
    conn.commit()

```

To retrieve data after executing a SELECT statement, you can treat the cursor as an iterator:

``` python

with sqlite3_connect('example.db') as [conn, c]:
    for row in c.execute('SELECT * FROM stocks ORDER BY price'):
        print(row)

```

> you can also call the cursor’s `fetchone()` method to retrieve a single matching row, or call `fetchall()` to get a list of the matching rows.

### Table metadata

We can access all the tables, their fields and their types using a simple query

``` python
with sqlite3_connect('example.db') as [conn, c]:
    rs = c.execute(
    """
    SELECT name, sql FROM sqlite_master
    WHERE type='table'
    ORDER BY name;
    """)
    
    for name, sql, *args in rs:
        print(name)
        print(sql)

```

###  SQLite and Python types
SQLite natively supports the following types: `NULL`, `INTEGER`, `REAL`, `TEXT`, `BLOB`.

The following Python types can thus be sent to SQLite without any problem:

| Python type | SQLite type |
|--|--|
| None | NULL |
| int | INTEGER |
| float | REAL |
| str | TEXT |
| bytes | BLOB |

SQLite supports only a limited set of types natively. To use other Python types with SQLite, you must adapt them to one of the sqlite3 module’s supported types for SQLite: one of `NoneType`, `int`, `float`, `str`, `bytes`.


### Custom types

there are two ways to read/write objects from a database:
1. encoding the object into a text/blob column using some format (often JSON)
2. converting objects into tables rows (respecting foreign keys as potential links to other objects)

with a DB-API based SQL interface like sqlite3, both of these methods can contain a lot of boiler plate code and so we will leave them out of thid tutorial
