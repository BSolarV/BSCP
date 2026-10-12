## 🎯 Target Location

**Primary Target:** Input fields protected by a Web Application Firewall (WAF), especially SQL injection, XSS, command injection, and path traversal.

## 🔥 Attack Flow

### Step 1: Identify the WAF

- Send a normal request, then a known test payload.
    
- Compare status codes, response bodies, and behavior.
    
- Look for `403 Forbidden`, CAPTCHA, block pages, or connection resets.
    
- Identify whether filtering occurs at the edge, application, or both.
    

### Step 2: Establish a Baseline

Record the original request, payload, response, and whether the vulnerability is exploitable without the WAF.

### Step 3: Try Bypass Techniques

#### 1. Hackvertor

Use Burp's **Hackvertor** extension to encode, transform, or nest payloads.
- Select the payload and use the Hackvertor context menu.
- Test transformations individually before combining them.
- Useful transformations include:
	- URL encoding
	- HTML entities
	- Unicode escapes
	- **hex encoding**
	- nested encoding, 
	depending on the context.
- Verify how the application decodes the payload; encoding alone does not guarantee a bypass.

#### 2. URL Encoding

Replace characters with `%HH` representations.

```
GET /search?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E HTTP/1.1
Host: target.com
```

**Double encoding:** Encode the percent sign again (`%` → `%25`). For example, `%3C` → `%253C`.

Test only when the application's decoding sequence could make this effective.

#### 3. HTML Entity Encoding

Useful when input is rendered in an HTML context.

```
&lt;script&gt;alert(1)&lt;/script&gt;
```

Numeric entities:

```
&#60;script&#62;alert(1)&#60;/script&#62;
```

**Note:** HTML entities are decoded by HTML parsing, not universally by server-side input filters. Effectiveness depends on the injection context.

#### 4. Case Variation

Some poorly implemented filters use case-sensitive matching.

```
UNION SELECT NULL
UnIoN SeLeCt NULL
```

```
<ScRiPt>alert(1)</ScRiPt>
```

Effective only if the backend accepts the variation and the WAF's detection is case-sensitive.

#### 5. Whitespace and Separator Variation

Test alternate token separators when the target syntax permits them.

```
UNION/**/SELECT NULL
UNION%09SELECT NULL
UNI%00ON SE%00LECT NULL
```

Other candidates include tabs, newlines, comments, and repeated whitespace. Support varies by database, parser, and encoding stage.

#### 6. Comment Insertion

Break up keywords when the target parser supports comments in the relevant position.

```
UN/**/ION SELECT NULL
```

Not every database accepts comments inside keywords. Prefer syntax verified against the target parser.

#### 7. Alternative Syntax

Change the payload's structure instead of merely encoding it.

- SQLi: alternate valid expressions, operators, or query structures.
    
- XSS: test context-appropriate HTML elements, attributes, and event handlers.
    
- Command injection: test permitted shell separators and alternate command syntax.
    
- Path traversal: test normalized path variants and encoding behavior.
    

The right alternative depends on the vulnerability and the backend.

#### 8. HTTP Request Variations

Test whether filtering differs between equivalent requests:

- Query parameters vs. form parameters vs. JSON fields.
    
- GET vs. POST, where the application supports both.
    
- Duplicate parameters with different values.
    
- Different `Content-Type` values accepted by the endpoint.
    
- Parameter ordering and whitespace variations.
    

Example:

```
POST /search HTTP/1.1
Host: target.com
Content-Type: application/json

{"query":"test"}
```

Compare against the same parameter submitted as form data. A different result may indicate inconsistent parsing or inspection.

#### 9. Unicode and Character Representation

Test alternate character representations only when the target parser supports them.

Examples include Unicode escapes in JSON:

```
{"query":"\u003cscript\u003e"}
```

Or percent-encoded path characters:

```
GET /files/%2e%2e%2fsecret.txt HTTP/1.1
Host: target.com
```

Check normalization and decoding behavior before assuming a bypass.

#### 10. Content-Type and Parser Differences

Some WAFs inspect a request differently depending on its declared format.

Compare supported formats such as:

```
Content-Type: application/x-www-form-urlencoded
```

```
Content-Type: application/json
```

```
Content-Type: multipart/form-data; boundary=----test
```

Change the body to match the declared format. A header-only change may cause rejection or parsing errors rather than a bypass.

## 🧪 Testing Strategy

1. **Baseline:** Confirm the original payload is blocked.
    
2. **Single transformation:** Try one encoding or syntax variation.
    
3. **Compare:** Record status code, response body, and application behavior.
    
4. **Combine:** Only combine transformations that individually provide useful evidence.
    
5. **Validate:** Confirm the intended vulnerability actually executes; a `200 OK` alone is not proof.
    
6. **Document:** Record the successful payload, encoding sequence, request format, and decoding behavior.
    

## 🎓 Exam Tips

1. Use Hackvertor to transform payloads quickly; understand what each transformation does.
    
2. Do not randomly stack encodings. Determine which component decodes the input and when.
    
3. Try syntax variation when encoding fails.
    
4. Test alternative parameter locations and supported content types.
    
5. Distinguish a WAF block from application-level rejection.
    
6. A successful bypass must demonstrate the underlying vulnerability, not merely avoid a `403`.
    
7. Prioritize techniques appropriate to the vulnerability and injection context.
    

**Key principle:** A WAF bypass exploits a difference between how the WAF interprets a request and how the application or backend ultimately processes it.