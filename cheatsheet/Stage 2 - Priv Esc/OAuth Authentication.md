## 🎯 Target Location

**Primary Target:** Sign-in page with OAuth integration

## 🔥 Attack Techniques

### Lab 1: Authentication Bypass via Implicit Flow
**Bypass:** Modify email/username in POST request

**Attack Flow:**
1. Intercept OAuth authentication process
2. Find `/authenticate` POST request
3. Change to victim's credentials

---

### Lab 2: Forced OAuth Profile Linking
**Bypass:** CSRF on OAuth linking (missing `state` parameter)

**Attack Flow:**
1. Start OAuth linking process
2. Intercept `/oauth-linking?code=abc123` request
3. Generate CSRF PoC
4. Send to victim

---

### Lab 3: Account Hijacking via redirect_uri
**Bypass:** Manipulate `redirect_uri` to steal authorization code

**Attack Flow:**
1. Change `redirect_uri` to your server
2. Generate CSRF PoC
3. Send to victim
4. Check Burp Collaborator for code

---

### Lab 4: Stealing Access Tokens via Open Redirect
**Bypass:** Combine open redirect with OAuth

**Step 1:** Find open redirect
**Step 2:** Chain with OAuth redirect_uri
**Step 3:** Extract token from fragment identifier

## 🎓 Exam Tips

1. **Look for OAuth sign-in** - "Login with..." buttons
2. **Intercept entire OAuth flow** - Multiple requests involved
3. **Check for `state` parameter** - Missing = CSRF vulnerability
4. **Test `redirect_uri` validation** - Try your server

---
