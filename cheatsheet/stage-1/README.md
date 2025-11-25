# Stage 1: Get Access to Any User

## Overview
Stage 1 focuses on obtaining access to any user account. Common attack vectors include XSS, authentication bypasses, and cache poisoning.

## Common Vulnerability Locations

### 🔍 Search Input
- Reflected XSS
- DOM-based XSS
- Always scan with Burp Scanner

### 💬 Comment Section
- Stored XSS
- DOM-based XSS
- **Note:** Comment section might be disabled on exam

### 🔐 Password Reset
- Host header poisoning
- Password reset logic flaws
- Email parameter manipulation

### 📧 Feedback Forms
- XSS in email/message fields
- HTTP request smuggling

## Vulnerability Checklist

- [ ] XSS in search functionality
- [ ] XSS in comment sections
- [ ] DOM-based XSS vulnerabilities
- [ ] Host header attacks on password reset
- [ ] Web cache poisoning
- [ ] HTTP request smuggling
- [ ] Authentication bypass techniques

## Key Tools

- **Burp Scanner** - Target scan on search and comment inputs
- **Burp Collaborator** - For out-of-band XSS
- **DOM Invader** - For DOM-based vulnerabilities

## Quick Tips

1. **Always scan search and comment inputs first**
2. **Use Burp Collaborator for cookie stealing**
3. **Check password reset functionality for host header injection**
4. **Look for cache poisoning opportunities**
5. **Test for HTTP request smuggling on feedback forms**

---

See individual vulnerability files for detailed payloads and techniques.
