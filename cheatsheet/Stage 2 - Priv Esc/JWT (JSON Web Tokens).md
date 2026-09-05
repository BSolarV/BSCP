## 🎯 Overview

JWT attacks focus on manipulating the token structure, signature, or claims to escalate privileges.

## 🛠️ Essential Tools

**JWT Editor (Burp Extension)** - Essential for JWT manipulation

## 🔥 Attack Techniques

### Lab 1: Unverified Signature
**Bypass:** Server doesn't verify signature at all

**Steps:**
1. Decode JWT
2. Modify payload (e.g., `"sub": "administrator"`)
3. Keep signature as-is or remove it

---

### Lab 2: Flawed Signature Verification (Algorithm Confusion)
**Bypass:** Change `RS256` to `HS256`

**Steps:**
1. Get server's public key
2. Change `"alg": "RS256"` to `"alg": "HS256"`
3. Sign token with public key (using HS256)

---

### Lab 3: Weak Signing Key (Brute Force)
**Bypass:** Crack weak secret key

**Using hashcat:**
```bash
hashcat -a 0 -m 16500 <jwt> /path/to/wordlist.txt
```

---

### Lab 4: JWK Header Injection
**Bypass:** Inject your own public key in JWT header

**Steps:**
1. Generate RSA key pair
2. Modify JWT payload
3. Add `jwk` parameter to header with your public key
4. Sign with your private key

---

### Lab 5: JKU Header Injection
**Bypass:** Point to your own JWK Set URL

**Steps:**
1. Generate RSA key pair
2. Create JWK Set on exploit server
3. Modify JWT header with `jku` parameter
4. Sign with your private key

---

### Lab 6: KID Header Path Traversal
**Bypass:** Use path traversal in `kid` parameter

**Technique:**
```json
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "../../../../../../dev/null"
}
```

---

### Lab 7: Algorithm None
**Bypass:** Set algorithm to `none`

**Steps:**
1. Change `"alg": "HS256"` to `"alg": "none"`
2. Remove signature completely

## 🎓 Exam Tips

1. **Install JWT Editor extension** - Essential tool
2. **Check for `/jwks.json`** - Common key location
3. **Try algorithm confusion first** - RS256 → HS256
4. **Look for `kid` parameter** - Path traversal opportunity
5. **Test `none` algorithm** - Simple but effective

---
