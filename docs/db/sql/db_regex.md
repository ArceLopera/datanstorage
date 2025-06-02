## What is Regex?
Regular expressions (regex) are patterns used to match character combinations within strings. In SQL, regex can be used to perform advanced search operations based on string patterns.

---

## Regex in MySQL
MySQL provides the `REGEXP` (or its synonym `RLIKE`) operator to perform regex-based matching in SQL queries.

**Syntax Example:**
```sql
SELECT * FROM users WHERE name REGEXP '^A';
```
This query selects all records from the `users` table where the `name` starts with the letter 'A'.

---

## Using Regex in PHP with MySQLi

### Example Table: `users`
| id | name       | email               |
|----|------------|---------------------|
| 1  | Alice      | alice@email.com      |
| 2  | Bob        | bob@example.com      |
| 3  | Annabelle  | annabelle@site.com   |

---

### Step 1: Connect to the Database
```php
<?php
$mysqli = new mysqli("localhost", "username", "password", "database");

if ($mysqli->connect_error) {
    die("Connection failed: " . $mysqli->connect_error);
}
?>
```

---

### Step 2: Execute a Regex Query
Example query to fetch users whose names start with 'A':
```php
<?php
$query = "SELECT * FROM users WHERE name REGEXP '^A'";
$result = $mysqli->query($query);

while ($row = $result->fetch_assoc()) {
    echo $row['name'] . "<br>";
}
?>
```

---

### Step 3: Use User Input Safely with Regex
It is important to sanitize user input to prevent SQL injection or malformed queries, especially when dealing with regex patterns.

Since MySQLi prepared statements do not support binding regex patterns directly, input should be safely escaped.

```php
<?php
$pattern = 'A'; // Example user input
$pattern = $mysqli->real_escape_string($pattern);

$query = "SELECT * FROM users WHERE name REGEXP '^$pattern'";
$result = $mysqli->query($query);

while ($row = $result->fetch_assoc()) {
    echo $row['name'] . "<br>";
}
?>
```

---

## Common Regex Patterns in MySQL

| Pattern            | Description                        |
|:------------------|:------------------------------------|
| `^A`               | Starts with 'A'                     |
| `e$`               | Ends with 'e'                       |
| `^[A-C]`           | Starts with 'A', 'B', or 'C'        |
| `b.t`              | 'b' followed by any character and 't' |
| `^.{5,10}$`        | String length between 5 and 10 characters |

Example usage:
```php
$query = "SELECT * FROM users WHERE name REGEXP 'b.t'";
```

---

## Best Practices
- Always sanitize user input using `$mysqli->real_escape_string()`.
- Avoid directly injecting untrusted regex patterns into queries.
- Use regex in SQL queries for search functionality that requires complex pattern matching.
- Validate the regex pattern on the server side before using it in a query if it is user-provided.

---

## Complete Example: User Search with Regex
```php
<?php
$mysqli = new mysqli("localhost", "username", "password", "database");

if ($mysqli->connect_error) {
    die("Connection failed: " . $mysqli->connect_error);
}

$startLetter = 'A';
$pattern = $mysqli->real_escape_string($startLetter);

$query = "SELECT * FROM users WHERE name REGEXP '^$pattern'";
$result = $mysqli->query($query);

while ($row = $result->fetch_assoc()) {
    echo $row['name'] . " - " . $row['email'] . "<br>";
}

$mysqli->close();
?>
```

---
