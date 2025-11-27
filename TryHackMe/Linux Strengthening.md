# Hashing

It is one way, bhako hash ko chai decrypt garna mildaina to plain text

If it is breached then we can match the hash to find the plain text

But can be bruteforced

we can also use rainbow tables

Salt ensures that the hash will be unique

ex: bcrypt(passwd, salt, cost), scrypt, argon2

MD5 and SHA1 can be vulnerable to collision attacks

We can use `john` or `hashcat` as usual to crack the passwords from the hash file

interestingly there are also `rcrack` and `oclhashcat` that can be used

here [https://en.wikipedia.org/wiki/Rainbow_table](https://en.wikipedia.org/wiki/Rainbow_table)

or just for ease of use we can use [https://crackstation.net/](https://crackstation.net/)

---

now for the main part

identifying the hashes was really ease just use `hashid hash` and then we

ssh’d to

```jsx
sarah@machineip: rainbowtree1230x
```

then we found the files using

```jsx
find / -type f -name "hash_file" 2>/dev/null
```

then we also used `crackstation` for finding the hashed passwds but using john and hashcat seemed fun

using john and hashcat we used the common rockyou.txt for the first two hashes

```jsx
hashcat -a 0 -m 0 hash1_1601657952696.txt /usr/share/wordlists/rockyou.txt
hashcat -a 0 -m 900 f9d4049dd6a4dc35d40e5265954b2a46 /usr/share/wordlists/rockyou.txt\\n
```

for the last one we had to use the wordlist from the ssh server

so we transferred the file via `scp` as it has `ssh`

```jsx
scp sarah@10.10.168.178:/home/sarah/system\\ AB//db/ww.mnf ./ww.mnf
```

```jsx
hashcat -a 0 -m 1400 ./hashC ./ww.mnf
```

then we found out the passwords

# base64

So for this we found the file encoded.txt

```jsx
sarah@james:~/system AB/managed$ find / -type f -name "encoded.txt" 2>/dev/null

```

then we came across this file and then there was something called `special`

so we grepped `answer` for safe grep

```jsx
	sarah@james:~/system AB/managed$ cat encoded.txt | base64 -d | grep super 
```

we found out that we had to go to the file

```jsx
sarah@james:~/system AB/managed$ cat encoded.txt | base64 -d | grep answer
you know how to decode base64 data, well done. you deserve the answer but because this is the linux strength training room where you are intended to build your linux memory and skills, you will have to find it in this very long text file. Look for the keyword: 'special' in this very large text file.
Nullam nibh diam, gravida vestibulum mi sed, consectetur tincidunt nunc. Morbi pharetra turpis nec ligula pellentesque lobortis. Aenean sit amet ullamcorper turpis. Nam id magna sed felis facilisis accumsan. Aliquam cursus dolor eu enim maximus, eu malesuada sapien dignissim. Suspendisse ultrices condimentum nisi et pellentesque. Fusce ornare aliquet quam, eu efficitur elit facilisis et. Donec special: the answer is in a file called ent.txt, 
```

Then we saw on the last sentence that we had a `ent.txt` file so we find’d that

```jsx
sarah@james:~/system AB/managed$ find / -type f -name "ent.txt" 2>/dev/null

```

then we cat’d it

```jsx
sarah@james:~/system AB/managed$ cat /home/sarah/logs/zhc/ent.txt
bfddc35c8f9c989545119988f79ccc77

```

now we can either [crackstation.net](http://crackstation.net) it or maybe just `john` it

we tried a lotta formats but it was….

```jsx
 sudo john --format=Raw-MD4 based --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD4 [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=20
Press 'q' or Ctrl-C to abort, almost any other key for status
john             (?)     
1g 0:00:00:00 DONE (2025-05-20 16:05) 100.0g/s 691200p/s 691200c/s 691200C/s horoscope..better
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

`john the don`

# Encryption/Decryption

We have another tool for encryption and signing keys that is `gpg`

just using `find` again and again to find the flag that was base64 encoded

and we got the flag

```jsx
echo "MS4gRmluZCBhIGZpbGUgY2FsbGVkIGxheWVyMS50eHQsIGl0cyBwYXNzd29yZCBpcyBoYWNrZWQu" | base64 -d

```

sweet thing that I found out:

|**Command**|**What It Does**|**Typical Use Case**|
|---|---|---|
|**`tail`**|Shows the last N lines of a file (default: 10)|Quickly view the most recent entries in a log or file[3](https://www.tutorialspoint.com/the-head-and-tail-commands-in-linux)[6](https://www.freecodecamp.org/news/helpful-linux-commands-you-should-know/)[8](https://www.linuxforfreshers.com/p/blog-page_2.html)|
|**`tac`**|Outputs the entire file, but with lines in reverse order (last line first, first line last)|See the whole file or a portion in reverse; useful for reversing logs, config files, or any text content|

for this

```jsx
  344  cd gpg2john/
  345  ls
  346  scp sarah@10.10.106.176://home/sarah/oldLogs/units/personal.txt.gpg .
  347  scp sarah@10.10.106.176://home/sarah/logs/zmn/old stuff/-mvLp/data.txt
  348  .
  349  scp sarah@10.10.106.176:/home/sarah/logs/zmn/old stuff/-mvLp/data.txt
  350  scp sarah@10.10.106.176:/home/sarah/logs/zmn/old\\ stuff/-mvLp/data.txt
  351  scp sarah@10.10.106.176:/home/sarah/logs/zmn/old\\ stuff/-mvLp/data.txt .
  352  ls -la
  353  cat data.txt 
  354  cp data.txt wordlist.txt
  355  ls
  356  gpg personal.txt.gpg 
  357  gpg2john personal.txt.gpg 
  358  ls
  359  tac wordlist.txt > wordlist.txt 
  360  cat wordlist.txt 
  361  cat data.txt 
  362  ls
  363  tac data.txt > wordlist.txt 
  364  cat wordlist.txt 
  365  sudo john wordlist=./wordlist.txt --format=gpg personal.txt.gpg 
  366  sudo john --wordlist=./wordlist.txt --format=gpg personal.txt.gpg 
  367  cat personal.txt.gpg 
  368  sudo gpg2john personal.txt.gpg person
  369  ls
  370  cat personal.txt.gpg 
  371  sudo gpg2john personal.txt.gpg > person
  372  ls
  373  sudo john --wordlist=./wordlist.txt --format=gpg person
  374  ls
  375  cat person
  376  file person
  377  gpg personal.txt.gpg 
  378  ls
  379  cat person
  380  cat personal.txt

```

# My SQL

we can login to mysql as `root` ?

**Restrict file permissions** on configuration files (e.g., **`/etc/mysql/my.cnf`**)

backup using `mysqldump`

## Basic commands

|**Action**|**SQL Command Example**|
|---|---|
|Show all databases|**`SHOW DATABASES;`**|
|Create a database|**`CREATE DATABASE bookstore;`**|
|Use a database|**`USE bookstore;`**|
|Show tables|**`SHOW TABLES;`**|
|Create a table|**`CREATE TABLE books (id INT AUTO_INCREMENT PRIMARY KEY, title VARCHAR(255), author VARCHAR(255), price DECIMAL(10,2));`**|
|Insert data|**`INSERT INTO books (title, author, price) VALUES ('1984', 'George Orwell', 8.99);`**|
|Select data|**`SELECT * FROM books;`**|
|Update data|**`UPDATE books SET price = 9.99 WHERE id = 1;`**|
|Delete data|**`DELETE FROM books WHERE id = 1;`**|
|Exit MySQL|**`exit`**|
|Show available dbs|`SHOW DATABASES`|
|Use a file on the server|`source file.sql`|
|Choosing a db|`USE dbname`|
|Describing the dbs|`DESBRIBE tablename`|
|||

[Sockets](https://www.notion.so/Sockets-1fa9c541337a80669278fd297e4f6d26?pvs=21)

[Trying out mysql (symlink mariadb)](https://www.notion.so/Trying-out-mysql-symlink-mariadb-1fa9c541337a8069878be55819786c8e?pvs=21)

```jsx
CREATE TABLE employees (
    emp_no      INT             NOT NULL,
    birth_date  DATE            NOT NULL,
    first_name  VARCHAR(14)     NOT NULL,
    last_name   VARCHAR(16)     NOT NULL,
    gender      ENUM ('M','F')  NOT NULL,    
    hire_date   DATE            NOT NULL,
    PRIMARY KEY (emp_no)
);

```

```jsx
on 
/home/shared/sql/ directory
passwd: danepon

James's SSH passwd maybe on the db
james may have root access

on 
/home/shared/sql/conf
conf file of about  50 mb 
This configuration file contained the directory location 
of a wordlist it used to randomly select a password from for
 encrypting the sql back-up copies with.
 
 passwd begins with `ebq`
 

```

On one of the files `JKpN` there written

Wordlist directory: aG9tZS9zYW1lZXIvSGlzdG9yeSBMQi9sYWJtaW5kL2xhdGVzdEJ1aWxkL2NvbmZpZ0JEQgo=

which on cyberchef gives `home/sameer/History LB/labmind/latestBuild/configBDB`

so on the dir we grepped `"^ebq"` and we got three files with list so

we have two options imo to just cat append the files to one big file or just find “^ebq” ones and then make a new file

yeah so we made the file and we went back to the `/home/shared/sql/conf/` dir and downloaded the file

`scp` bata try garda bhayena so I tried it using `sftp` and it worked

then we used `gpg2john` ? bhayena file nai large bhayexa

lol

but etikai manually nai garde

ani after that I unzipped it and then went to the dir and mysql haneko

aafai session disconnect bhayo tait

aba bholi garamla

okay soo feri try garau

passwd tha thiyo so we opened the zipped file and then we got into the db ani searched for the first_name on the employees db

then we got the password

the last part was fairly simple ssh to james `sudo su`

then find the file and yay the flag was found

Time taken to compete

`around 4 days`