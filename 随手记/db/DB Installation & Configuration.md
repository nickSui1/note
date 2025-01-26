# Installation & Configuration

## MySQL

To download and install MySQL on macOS using the terminal, you can either use **Homebrew** (for supported versions) or manually download the package. Here's a step-by-step guide:

------

### **Option 1: Install MySQL with Homebrew**

1. **Check for Available MySQL Versions** Run the following command to see which MySQL versions are available:

   ```bash
   brew search mysql
   ```

2. **Install MySQL 8.0 (or Latest Supported Version)** If you're fine with using the latest version:

   ```bash
   brew install mysql
   ```

3. **Start MySQL Service** After installation, start MySQL:

   ```bash
   brew services start mysql
   ```

4. **Secure Installation** Secure your MySQL installation by setting the root password and other options:

   ```bash
   mysql_secure_installation
   ```

5. **Connect to MySQL** Connect to MySQL using the command:

   ```bash
   mysql -u root -p
   ```

------

### **Option 2: Manually Download MySQL from the Official Site**

If you need a specific version, follow these steps:

1. **Download MySQL** Go to the [MySQL Community Server Archives](https://downloads.mysql.com/archives/community/) and download the desired version for macOS.

2. **Install the Downloaded Package** After downloading, open the `.dmg` file and follow the installation instructions.

3. **Add MySQL to PATH (Optional)** To use MySQL in the terminal, add its binary path to your shell configuration file (e.g., `.zshrc` or `.bash_profile`):

   ```bash
   echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.zshrc
   source ~/.zshrc
   ```

4. **Start MySQL** Start the MySQL server:

   ```bash
   sudo /usr/local/mysql/support-files/mysql.server start
   ```

5. **Secure Installation** Secure your MySQL installation:

   ```bash
   mysql_secure_installation
   ```

6. **Connect to MySQL** Use the MySQL client to connect:

   ```bash
   mysql -u root -p
   ```

------

### **Option 3: Use Docker for MySQL Installation**

If you'd prefer to avoid manual installation, use Docker:

1. Pull the MySQL image:

   ```bash
   docker pull mysql:latest
   ```

2. Run the MySQL container:

   ```bash
   docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 mysql:latest
   ```

3. Access MySQL using the terminal:

   ```bash
   mysql -h 127.0.0.1 -u root -p
   ```

------

### MySQL Configuration

To start the MySQL server from the terminal on macOS, the command depends on how MySQL was installed. Here are the common methods:

---

**1. If MySQL was Installed via Homebrew**

1. Start the MySQL server:

   ```bash
   brew services start mysql
   ```

2. Check the status of the service:

   ```bash
   brew services list
   ```

3. Stop the MySQL server (if needed):

   ```bash
   brew services stop mysql
   ```

---

**2. If MySQL was Installed via the `.dmg` Package**

1. Start the MySQL server manually:

   ```bash
   sudo /usr/local/mysql/support-files/mysql.server start
   ```

2. Stop the MySQL server:

   ```bash
   sudo /usr/local/mysql/support-files/mysql.server stop
   ```

3. Check the server status:

   ```bash
   sudo /usr/local/mysql/support-files/mysql.server status
   ```

---

**3. If MySQL is Running in a Docker Container**

1. Start the MySQL container:

   ```bash
   docker start <container_name_or_id>
   ```

2. Check running containers:

   ```bash
   docker ps
   ```

3. Stop the MySQL container:

   ```bash
   docker stop <container_name_or_id>
   ```

   

---

**4. General Approach**

If you're unsure how MySQL was installed, try locating the `mysql.server` file:

1. Find the `mysql.server` script:

   ```bash
   find /usr/local -name "mysql.server"
   ```

2. Use the script to start MySQL:

   ```bash
   sudo <path_to_mysql.server> start
   ```

Replace `<path_to_mysql.server>` with the full path from the previous command's output.

---

**Verify MySQL is Running**

Once started, check if MySQL is running by connecting to it:

```bash
mysql -u root -p
```

### User Management

To create a new user in MySQL and grant appropriate permissions, follow these steps:

---

**1. Log In to MySQL**

Use the MySQL root user to log in:

```bash
mysql -u root -p
```

---

**2. Create a New User**

Run the following SQL command to create a new user. Replace `new_user` with the username and `password` with the desired password:

```sql
CREATE USER 'new_user'@'localhost' IDENTIFIED BY 'password';
```

- `'new_user'@'localhost'`: The user can only connect from the local machine.
- For remote access, use `'new_user'@'%'` instead of `'localhost'`.

---

**3. Grant Permissions**

Assign privileges to the user. For example:

- **Grant All Privileges on a Specific Database**:

  ```sql
  GRANT ALL PRIVILEGES ON database_name.* TO 'new_user'@'localhost';
  ```

  Replace `database_name` with the actual database name.

- **Grant All Privileges Globally** (Not recommended for security reasons):

  ```sql
  GRANT ALL PRIVILEGES ON *.* TO 'new_user'@'localhost' WITH GRANT OPTION;
  ```

- **Grant Specific Privileges**: For example, only allow `SELECT` and `INSERT` on a specific database:

  ```sql
  GRANT SELECT, INSERT ON database_name.* TO 'new_user'@'localhost';
  ```

---

**4. Apply Changes**

Flush the privileges to make the changes effective:

```sql
FLUSH PRIVILEGES;
```

---

**5. Verify the User**

Exit MySQL and try logging in with the new user:

```bash
mysql -u new_user -p
```

---

**6. Optional: Remove a User**

If you need to remove the user later, use:

```sql
DROP USER 'new_user'@'localhost';
```



### Execute sql file to remote MySQL server

``` bash
mysql -u <username> -p -h <remote_host> -P <port> <database_name> < /path/to/file.sql
```

#### example

```bash
mysql -u admin -p -h 8.141.85.27 -P 3306 testdb < /home/user/scripts/init.sql
```

