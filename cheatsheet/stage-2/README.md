# Stage 2: Privilege Escalation

## Overview
Stage 2 focuses on escalating privileges from a regular user to administrator or stealing admin credentials/data.

## Common Vulnerability Locations

### 🔍 Advanced Search
- SQL Injection in search parameters
- Most common priv esc vector

### 👤 Profile Update
- JSON role manipulation
- Access control bypasses
- CSRF on email change

### 🍪 Cookies
- JWT manipulation
- Session token analysis
- Insecure deserialization

### 🔐 OAuth/Authentication
- OAuth flow manipulation
- 2FA bypasses

## Vulnerability Checklist

- [ ] SQL Injection in advanced search
- [ ] JSON roleId manipulation in profile
- [ ] CSRF on password/email change
- [ ] JWT signature bypass
- [ ] JWT header injection (jwk, jku, kid)
- [ ] OAuth redirect_uri manipulation
- [ ] Access control flaws (IDOR, role checks)
- [ ] Insecure deserialization in cookies

## Key Tools

- **SQLMap** - `--level 5 --risk 3` for comprehensive testing
- **JWT Editor** - Burp extension for JWT manipulation
- **Hackvertor** - For encoding bypasses

## Quick Tips

1. **Check for Advanced Search page** - High probability of SQL injection
2. **Inspect JSON in profile updates** - Look for `roleId`, `isAdmin` fields
3. **Test CSRF on email change** - Then reset admin password
4. **Analyze JWTs** - Check algorithm, signature, claims
5. **Use SQLMap** - Don't waste time on manual SQL injection

## Common Patterns

### Pattern 1: SQL Injection → Admin Credentials
```
Advanced Search → SQLMap → Extract admin password → Login as admin
```

### Pattern 2: JSON Role Manipulation
```
Update Profile → Modify {"roleId": 2} → Become admin
```

### Pattern 3: CSRF → Password Reset
```
CSRF on email change → Change admin email → Reset password → Login
```

### Pattern 4: JWT Algorithm Confusion
```
Change "alg": "RS256" to "alg": "none" → Remove signature → Admin access
```

---

See individual vulnerability files for detailed payloads and techniques.
