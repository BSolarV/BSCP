# BSCP Exam Cheatsheet & Payloads

> **Personal cheatsheet for Burp Suite Certified Practitioner (BSCP) Exam**

## 📋 Exam Structure

The BSCP exam consists of **two web applications**, **two hours each**. Each application has three stages:

### Stage 1: Get Access to Any User
**Goal:** Obtain access to any user account

**Common Vulnerabilities:**
- XSS (Cross-Site Scripting)
- DOM-based vulnerabilities
- Authentication bypasses
- Web cache poisoning
- HTTP Host header attacks
- HTTP request smuggling

### Stage 2: Privilege Escalation
**Goal:** Promote yourself to administrator or steal admin data

**Common Vulnerabilities:**
- SQL Injection
- CSRF (Cross-Site Request Forgery)
- Insecure deserialization
- OAuth authentication flaws
- JWT attacks
- Access control vulnerabilities

### Stage 3: File System Access
**Goal:** Read `/home/carlos/secret` from the file system

**Common Vulnerabilities:**
- SSRF (Server-Side Request Forgery)
- XXE (XML External Entity) injection
- OS command injection
- SSTI (Server-Side Template Injection)
- Directory/Path traversal
- Insecure deserialization
- File upload vulnerabilities

## 🎯 Exam Strategy

1. **Scan Everything** - Use Burp Scanner on all functionality
2. **Focus on Common Patterns** - Check search inputs, comment sections, feedback forms
3. **Time Management** - 2 hours per app, don't get stuck on one vulnerability
4. **Burp Collaborator** - Always have it ready for out-of-band attacks
5. **SQLMap** - Use `--level 5 --risk 3` for comprehensive SQL injection testing

## 📁 Directory Structure

```
BSCP/
├── cheatsheet/
│   ├── stage-1/          # Access vulnerabilities
│   ├── stage-2/          # Privilege escalation
│   └── stage-3/          # File system access
├── payloads/             # Ready-to-use payloads
└── wordlists/            # Custom wordlists
```

## 🔗 Quick Links

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [BSCP Exam Guide](https://portswigger.net/web-security/certification)

## ⚡ Quick Reference

| Stage | Primary Targets | Tools |
|-------|----------------|-------|
| 1 | Search, Comments, Login | Burp Scanner, XSS Validator |
| 2 | Admin Panel, Profile Update | SQLMap, JWT Tool |
| 3 | File Upload, Feedback Forms | Burp Collaborator, XXE Tools |

## 🚀 Getting Started

1. Review vulnerability-specific cheatsheets in `/cheatsheet/`
2. Practice with payloads in `/payloads/`
3. Complete all PortSwigger Academy labs
4. Take practice exams

---


---

**Good luck on your BSCP exam! 🎓**
