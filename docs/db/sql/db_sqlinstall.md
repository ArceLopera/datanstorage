## **Installing & Setting Up SQL**  

### ** Installing SQLite (Lightweight, No Server Needed)**  
💡 **SQLite** is great for local development and small applications.  
- Install SQLite:  
  ```sh
  sudo apt install sqlite3  # Linux
  brew install sqlite3  # macOS
  ```
- Open SQLite CLI:  
  ```sh
  sqlite3 my_database.db
  ```
- Verify installation:  
  ```sql
  SELECT sqlite_version();
  ```

### ** Installing MySQL (Popular for Web Apps)**  
- Install MySQL Server:  
  ```sh
  sudo apt install mysql-server  # Linux
  brew install mysql  # macOS
  ```
- Start MySQL and login:  
  ```sh
  mysql -u root -p
  ```

### ** Installing PostgreSQL (Advanced & Scalable)**  
- Install PostgreSQL:  
  ```sh
  sudo apt install postgresql  # Linux
  brew install postgresql  # macOS
  ```
- Start PostgreSQL and login:  
  ```sh
  psql -U postgres
  ```
---
