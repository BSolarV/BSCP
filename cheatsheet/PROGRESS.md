# All Labs

| Category                       | Solved | Not Solved | Total | Completion % |
| ------------------------------ | ------ | ---------- | ----- | ------------ |
| SQL injection                  | 9      | 7          | 16    | 56.3%        |
| Cross-site scripting           | 16     | 0          | 16    | 100%         |
| CSRF                           | 11     | 0          | 11    | 100%         |
| Clickjacking                   | 0      | 4          | 4     | 0%           |
| DOM-based vulnerabilities      | 5      | 0          | 5     | 100%         |
| CORS                           | 1      | 0          | 1     | 100%         |
| XXE injection                  | 6      | 0          | 6     | 100%         |
| SSRF                           | 3      | 0          | 3     | 100%         |
| HTTP request smuggling         | 14     | 1          | 15    | 93.3%        |
| OS command injection           | 4      | 0          | 4     | 100%         |
| Server-side template injection | 5      | 0          | 5     | 100%         |
| Path traversal                 | 5      | 0          | 5     | 100%         |
| Access control                 | 4      | 0          | 4     | 100%         |
| Authentication                 | 8      | 1          | 9     | 88.9%        |
| WebSockets                     | 0      | 2          | 2     | 0%           |
| Web cache poisoning            | 9      | 0          | 9     | 100%         |
| Insecure deserialization       | 5      | 1          | 6     | 83.3%        |
| Information disclosure         | 0      | 1          | 1     | 0%           |
| Business logic                 | 2      | 5          | 7     | 28.6%        |
| HTTP Host header attacks       | 4      | 0          | 4     | 100%         |
| OAuth authentication           | 4      | 0          | 4     | 100%         |
| File upload vulnerabilities    | 4      | 0          | 4     | 100%         |
| JWT                            | 4      | 0          | 4     | 100%         |
| Essential skills               | 2      | 0          | 2     | 100%         |
| Prototype pollution            | 0      | 9          | 9     | 0%           |
| GraphQL API                    | 4      | 0          | 4     | 100%         |
| Race conditions                | 0      | 4          | 4     | 0%           |
| NoSQL injection                | 0      | 2          | 2     | 0%           |
| API testing                    | 3      | 0          | 3     | 100%         |
| Web LLM attacks                | 0      | 2          | 2     | 0%           |
| Web cache deception            | 0      | 3          | 3     | 0%           |

**SQL injection**
- SQL injection attack, querying the database type and version on Oracle - Solved
- SQL injection attack, querying the database type and version on MySQL and Microsoft - Solved
- SQL injection attack, listing the database contents on non-Oracle databases - Solved
- SQL injection attack, listing the database contents on Oracle - Solved
- SQL injection UNION attack, determining the number of columns returned by the query - Solved
- SQL injection UNION attack, finding a column containing text - Solved
- SQL injection UNION attack, retrieving data from other tables - Solved
- SQL injection UNION attack, retrieving multiple values in a single column - Solved
- Blind SQL injection with conditional responses - Not solved
- Blind SQL injection with conditional errors - Not solved
- Visible error-based SQL injection - Not solved
- Blind SQL injection with time delays - Solved
- Blind SQL injection with time delays and information retrieval - Not solved
- Blind SQL injection with out-of-band interaction - Not solved
- Blind SQL injection with out-of-band data exfiltration - Not solved
- SQL injection with filter bypass via XML encoding - Not solved

**Cross-site scripting**
- DOM XSS in document.write sink using source location.search inside a select element - Solved
- DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded - Solved
- Reflected DOM XSS - Solved
- Stored DOM XSS - Solved
- Reflected XSS into HTML context with most tags and attributes blocked - Solved
- Reflected XSS into HTML context with all tags blocked except custom ones - Solved
- Reflected XSS with some SVG markup allowed - Solved
- Reflected XSS in canonical link tag - Solved
- Reflected XSS into a JavaScript string with single quote and backslash escaped - Solved
- Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped - Solved
- Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped - Solved
- Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped - Solved
- Exploiting cross-site scripting to steal cookies - Solved
- Exploiting cross-site scripting to capture passwords - Solved
- Exploiting XSS to bypass CSRF defenses - Solved
- Reflected XSS protected by very strict CSP, with dangling markup attack - Solved

**Cross-site request forgery (CSRF)**
- CSRF where token validation depends on request method - Solved
- CSRF where token validation depends on token being present - Solved
- CSRF where token is not tied to user session - Solved
- CSRF where token is tied to non-session cookie - Solved
- CSRF where token is duplicated in cookie - Solved
- SameSite Lax bypass via method override - Solved
- SameSite Strict bypass via client-side redirect - Solved
- SameSite Strict bypass via sibling domain - Solved
- SameSite Lax bypass via cookie refresh - Solved
- CSRF where Referer validation depends on header being present - Solved
- CSRF with broken Referer validation - Solved

**Clickjacking**
- Clickjacking with form input data prefilled from a URL parameter - Not solved
- Clickjacking with a frame buster script - Not solved
- Exploiting clickjacking vulnerability to trigger DOM-based XSS - Not solved
- Multistep clickjacking - Not solved

**DOM-based vulnerabilities**
- DOM XSS using web messages - Solved
- DOM XSS using web messages and a JavaScript URL - Solved
- DOM XSS using web messages and JSON.parse - Solved
- DOM-based open redirection - Solved
- DOM-based cookie manipulation - Solved

**CORS**
- CORS vulnerability with trusted insecure protocols - Solved

**XXE injection**
- Blind XXE with out-of-band interaction - Solved
- Blind XXE with out-of-band interaction via XML parameter entities - Solved
- Exploiting blind XXE to exfiltrate data using a malicious external DTD - Solved
- Exploiting blind XXE to retrieve data via error messages - Solved
- Exploiting XInclude to retrieve files - Solved
- Exploiting XXE via image file upload - Solved

**SSRF**
- Blind SSRF with out-of-band detection - Solved
- SSRF with blacklist-based input filter - Solved
- SSRF with filter bypass via open redirection vulnerability - Solved

**HTTP request smuggling**
- HTTP request smuggling, confirming a CL.TE vulnerability via differential responses - Solved
- HTTP request smuggling, confirming a TE.CL vulnerability via differential responses - Solved
- Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability - Solved
- Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability - Solved
- Exploiting HTTP request smuggling to reveal front-end request rewriting - Solved
- Exploiting HTTP request smuggling to capture other users' requests - Solved
- Exploiting HTTP request smuggling to deliver reflected XSS - Solved
- Response queue poisoning via H2.TE request smuggling - Solved
- H2.CL request smuggling - Solved
- HTTP/2 request smuggling via CRLF injection - Solved
- HTTP/2 request splitting via CRLF injection - Not solved
- CL.0 request smuggling - Solved
- HTTP request smuggling, basic CL.TE vulnerability - Solved
- HTTP request smuggling, basic TE.CL vulnerability - Solved
- HTTP request smuggling, obfuscating the TE header - Solved

**OS command injection**
- Blind OS command injection with time delays - Solved
- Blind OS command injection with output redirection - Solved
- Blind OS command injection with out-of-band interaction - Solved
- Blind OS command injection with out-of-band data exfiltration - Solved

**Server-side template injection**
- Basic server-side template injection - Solved
- Basic server-side template injection (code context) - Solved
- Server-side template injection using documentation - Solved
- Server-side template injection in an unknown language with a documented exploit - Solved
- Server-side template injection with information disclosure via user-supplied objects - Solved

**Path traversal**
- File path traversal, traversal sequences blocked with absolute path bypass - Solved
- File path traversal, traversal sequences stripped non-recursively - Solved
- File path traversal, traversal sequences stripped with superfluous URL-decode - Solved
- File path traversal, validation of start of path - Solved
- File path traversal, validation of file extension with null byte bypass - Solved

**Access control**
- URL-based access control can be circumvented - Solved
- Method-based access control can be circumvented - Solved
- Multi-step process with no access control on one step - Solved
- Referer-based access control - Solved

**Authentication**
- Username enumeration via subtly different responses - Solved
- Username enumeration via response timing - Solved
- Broken brute-force protection, IP block - Not solved
- Username enumeration via account lock - Solved
- 2FA broken logic - Solved
- Brute-forcing a stay-logged-in cookie - Solved
- Offline password cracking - Solved
- Password reset poisoning via middleware - Solved
- Password brute-force via password change - Solved

**WebSockets**
- Cross-site WebSocket hijacking - Not solved
- Manipulating the WebSocket handshake to exploit vulnerabilities - Not solved

**Web cache poisoning**
- Web cache poisoning with an unkeyed header - Solved
- Web cache poisoning with an unkeyed cookie - Solved
- Web cache poisoning with multiple headers - Solved
- Targeted web cache poisoning using an unknown header - Solved
- Web cache poisoning via an unkeyed query string - Solved
- Web cache poisoning via an unkeyed query parameter - Solved
- Parameter cloaking - Solved
- Web cache poisoning via a fat GET request - Solved
- URL normalization - Solved

**Insecure deserialization**
- Modifying serialized data types - Solved
- Using application functionality to exploit insecure deserialization - Solved
- Arbitrary object injection in PHP - Solved
- Exploiting Java deserialization with Apache Commons - Solved
- Exploiting PHP deserialization with a pre-built gadget chain - Solved
- Exploiting Ruby deserialization using a documented gadget chain - Not solved

**Information disclosure**
- Information disclosure in version control history - Not solved

**Business logic vulnerabilities**
- Low-level logic flaw - Not solved
- Inconsistent handling of exceptional input - Not solved
- Weak isolation on dual-use endpoint - Solved
- Insufficient workflow validation - Not solved
- Authentication bypass via flawed state machine - Solved
- Infinite money logic flaw - Not solved
- Authentication bypass via encryption oracle - Not solved

**HTTP Host header attacks**
- Web cache poisoning via ambiguous requests - Solved
- Routing-based SSRF - Solved
- SSRF via flawed request parsing - Solved
- Host validation bypass via connection state attack - Solved

**OAuth authentication**
- SSRF via OpenID dynamic client registration - Solved
- Forced OAuth profile linking - Solved
- OAuth account hijacking via redirect_uri - Solved
- Stealing OAuth access tokens via an open redirect - Solved

**File upload vulnerabilities**
- Web shell upload via path traversal - Solved
- Web shell upload via extension blacklist bypass - Solved
- Web shell upload via obfuscated file extension - Solved
- Remote code execution via polyglot web shell upload - Solved

**JWT**
- JWT authentication bypass via weak signing key - Solved
- JWT authentication bypass via jwk header injection - Solved
- JWT authentication bypass via jku header injection - Solved
- JWT authentication bypass via kid header path traversal - Solved

**Essential skills**
- Discovering vulnerabilities quickly with targeted scanning - Solved
- Scanning non-standard data structures - Solved

**Prototype pollution**
- Client-side prototype pollution via browser APIs - Not solved
- DOM XSS via client-side prototype pollution - Not solved
- DOM XSS via an alternative prototype pollution vector - Not solved
- Client-side prototype pollution via flawed sanitization - Not solved
- Client-side prototype pollution in third-party libraries - Not solved
- Privilege escalation via server-side prototype pollution - Not solved
- Detecting server-side prototype pollution without polluted property reflection - Not solved
- Bypassing flawed input filters for server-side prototype pollution - Not solved
- Remote code execution via server-side prototype pollution - Not solved

**GraphQL API vulnerabilities**
- Accidental exposure of private GraphQL fields - Solved
- Finding a hidden GraphQL endpoint - Solved
- Bypassing GraphQL brute force protections - Solved
- Performing CSRF exploits over GraphQL - Solved

**Race conditions**
- Bypassing rate limits via race conditions - Not solved
- Multi-endpoint race conditions - Not solved
- Single-endpoint race conditions - Not solved
- Exploiting time-sensitive vulnerabilities - Not solved

**NoSQL injection**
- Exploiting NoSQL injection to extract data - Not solved
- Exploiting NoSQL operator injection to extract unknown fields - Not solved

**API testing**
- Exploiting server-side parameter pollution in a query string - Solved
- Finding and exploiting an unused API endpoint - Solved
- Exploiting a mass assignment vulnerability - Solved

**Web LLM attacks**
- Exploiting vulnerabilities in LLM APIs - Not solved
- Indirect prompt injection - Not solved

**Web cache deception**
- Exploiting path delimiters for web cache deception - Not solved
- Exploiting origin server normalization for web cache deception - Not solved
- Exploiting cache server normalization for web cache deception - Not solved


